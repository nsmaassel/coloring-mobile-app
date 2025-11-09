# Contracts: Interface Definitions

**Feature**: 001-coloring-pages  
**Date**: 2025-11-09  
**Purpose**: Define protocol interfaces for modular architecture ensuring loose coupling between layers

---

## Overview

This directory contains Swift protocol definitions that establish contracts between different modules of the application. These interfaces support the **Feature Modularity** principle by allowing components to depend on abstractions rather than concrete implementations.

---

## Architecture Layers

```text
┌─────────────────────────────────────────┐
│            UI Layer (SwiftUI)           │
│  (Views, ViewModels, User Interactions) │
└─────────────────┬───────────────────────┘
                  │ depends on
                  ▼
┌─────────────────────────────────────────┐
│         Service Layer (Protocols)        │
│   (Repositories, DrawingEngine, etc.)   │
└─────────────────┬───────────────────────┘
                  │ implements
                  ▼
┌─────────────────────────────────────────┐
│      Implementation Layer (Concrete)     │
│  (File operations, PencilKit wrappers)  │
└─────────────────────────────────────────┘
```

---

## Contract Files

### 1. `ColoringPageRepository.swift` - Coloring Page Storage

**Purpose**: Manages loading and saving coloring page metadata and images

```swift
import Foundation
import UIKit

/// Protocol defining operations for coloring page persistence
protocol ColoringPageRepositoryProtocol {
    // MARK: - Read Operations
    
    /// Loads all coloring pages (built-in and custom)
    /// - Returns: Array of all available coloring pages
    /// - Throws: StorageError if metadata cannot be loaded
    func loadAll() async throws -> [ColoringPage]
    
    /// Loads a specific coloring page by ID
    /// - Parameter id: UUID of the coloring page
    /// - Returns: The requested coloring page
    /// - Throws: StorageError.fileNotFound if page doesn't exist
    func load(id: UUID) async throws -> ColoringPage
    
    /// Loads the image for a coloring page
    /// - Parameter coloringPage: The coloring page to load image for
    /// - Returns: The coloring page image
    /// - Throws: StorageError.fileNotFound if image doesn't exist
    func loadImage(for coloringPage: ColoringPage) async throws -> UIImage
    
    // MARK: - Write Operations
    
    /// Imports a custom coloring page from a UIImage
    /// - Parameters:
    ///   - image: The image to import
    ///   - name: Display name for the coloring page
    ///   - category: Optional category/tag
    /// - Returns: The newly created ColoringPage
    /// - Throws: StorageError if import fails
    func importCustomPage(image: UIImage, name: String, category: String?) async throws -> ColoringPage
    
    /// Deletes a custom coloring page (cannot delete built-in pages)
    /// - Parameter id: UUID of the page to delete
    /// - Throws: StorageError.invalidColoringPage if attempting to delete built-in page
    func delete(id: UUID) async throws
    
    /// Updates coloring page metadata (name, category)
    /// - Parameter coloringPage: Updated coloring page
    /// - Throws: StorageError if update fails
    func update(_ coloringPage: ColoringPage) async throws
}

/// Concrete implementation using file-based storage
final class ColoringPageRepository: ColoringPageRepositoryProtocol {
    // MARK: - Properties
    
    private let fileManager: FileManager
    private let documentDirectory: URL
    private let metadataFilename = "metadata.json"
    
    // MARK: - Initialization
    
    init(fileManager: FileManager = .default) {
        self.fileManager = fileManager
        self.documentDirectory = fileManager.urls(for: .documentDirectory, in: .userDomainMask)[0]
    }
    
    // MARK: - Implementation
    
    // ... (implementation details in actual code)
}
```

**Contract Guarantees**:

- All operations are async to avoid blocking main thread
- Throws typed errors (StorageError) for clear error handling
- Built-in pages are immutable (cannot be deleted or modified)
- Custom pages are validated before saving

**Usage Example**:

```swift
// In ViewModel
class ColoringPageGalleryViewModel: ObservableObject {
    private let repository: ColoringPageRepositoryProtocol
    @Published var coloringPages: [ColoringPage] = []
    
    init(repository: ColoringPageRepositoryProtocol) {
        self.repository = repository
    }
    
    func loadPages() async {
        do {
            coloringPages = try await repository.loadAll()
        } catch {
            // Handle error
        }
    }
}
```

---

### 2. `ArtworkRepository.swift` - Saved Artwork Storage

**Purpose**: Manages persistence of artwork (metadata, drawings, thumbnails)

```swift
import Foundation
import PencilKit
import UIKit

/// Protocol defining operations for artwork persistence
protocol ArtworkRepositoryProtocol {
    // MARK: - Read Operations
    
    /// Loads metadata for all saved artworks (without loading full drawings)
    /// - Returns: Array of artwork metadata sorted by modified date (newest first)
    /// - Throws: StorageError if loading fails
    func loadAllMetadata() async throws -> [Artwork]
    
    /// Loads a specific artwork's metadata
    /// - Parameter id: UUID of the artwork
    /// - Returns: The artwork metadata
    /// - Throws: StorageError.fileNotFound if artwork doesn't exist
    func loadMetadata(id: UUID) async throws -> Artwork
    
    /// Loads the full PKDrawing for an artwork
    /// - Parameter id: UUID of the artwork
    /// - Returns: The PKDrawing data
    /// - Throws: StorageError if loading fails or data is corrupted
    func loadDrawing(id: UUID) async throws -> PKDrawing
    
    /// Loads the cached thumbnail for an artwork
    /// - Parameter id: UUID of the artwork
    /// - Returns: The thumbnail image
    /// - Throws: StorageError.fileNotFound if thumbnail doesn't exist
    func loadThumbnail(id: UUID) async throws -> UIImage
    
    // MARK: - Write Operations
    
    /// Saves a new or updated artwork
    /// - Parameters:
    ///   - artwork: The artwork metadata
    ///   - drawing: The PKDrawing data
    ///   - thumbnail: Generated thumbnail image
    /// - Throws: StorageError if save fails
    func save(artwork: Artwork, drawing: PKDrawing, thumbnail: UIImage) async throws
    
    /// Deletes an artwork and all associated files
    /// - Parameter id: UUID of the artwork to delete
    /// - Throws: StorageError if deletion fails
    func delete(id: UUID) async throws
    
    /// Updates only the metadata (e.g., title change without re-saving drawing)
    /// - Parameter artwork: Updated artwork metadata
    /// - Throws: StorageError if update fails
    func updateMetadata(_ artwork: Artwork) async throws
}

/// Concrete implementation using file-based storage + PencilKit
final class ArtworkRepository: ArtworkRepositoryProtocol {
    // MARK: - Properties
    
    private let fileManager: FileManager
    private let documentDirectory: URL
    
    // MARK: - Initialization
    
    init(fileManager: FileManager = .default) {
        self.fileManager = fileManager
        self.documentDirectory = fileManager.urls(for: .documentDirectory, in: .userDomainMask)[0]
    }
    
    // MARK: - Implementation
    
    // ... (implementation details in actual code)
}
```

**Contract Guarantees**:

- Metadata operations are separate from drawing operations (performance optimization)
- Thumbnails are generated and cached (not regenerated on every load)
- All file operations are atomic (temp file → rename pattern to avoid corruption)
- Validates artwork data before saving

**Usage Example**:

```swift
// In ViewModel
class DrawingCanvasViewModel: ObservableObject {
    private let repository: ArtworkRepositoryProtocol
    @Published var currentArtwork: Artwork?
    
    func saveCurrentDrawing(drawing: PKDrawing) async {
        guard var artwork = currentArtwork else { return }
        
        artwork.modifiedDate = Date()
        artwork.strokeCount = drawing.strokes.count
        
        let thumbnail = generateThumbnail(from: drawing)
        
        do {
            try await repository.save(artwork: artwork, drawing: drawing, thumbnail: thumbnail)
        } catch {
            // Handle error
        }
    }
}
```

---

### 3. `DrawingEngine.swift` - Drawing Canvas Management

**Purpose**: Abstracts PencilKit canvas operations for testability and potential future drawing engine swaps

```swift
import Foundation
import PencilKit
import UIKit

/// Protocol defining drawing canvas operations
protocol DrawingEngineProtocol {
    // MARK: - Canvas State
    
    /// Current drawing data
    var drawing: PKDrawing { get set }
    
    /// Currently selected tool (brush/eraser)
    var currentTool: PKInkingTool { get set }
    
    /// Whether the drawing has unsaved changes
    var hasUnsavedChanges: Bool { get }
    
    // MARK: - Tool Selection
    
    /// Sets the active brush with specified color
    /// - Parameters:
    ///   - color: The brush color
    ///   - size: The brush size (default: 10.0)
    func selectBrush(color: UIColor, size: CGFloat)
    
    /// Sets the eraser as the active tool
    /// - Parameter size: The eraser size (default: 20.0)
    func selectEraser(size: CGFloat)
    
    // MARK: - Drawing Operations
    
    /// Clears all strokes from the canvas
    func clearCanvas()
    
    /// Undoes the last drawing action
    /// - Returns: true if undo was performed, false if nothing to undo
    @discardableResult
    func undo() -> Bool
    
    /// Redoes the last undone action
    /// - Returns: true if redo was performed, false if nothing to redo
    @discardableResult
    func redo() -> Bool
    
    // MARK: - Export
    
    /// Generates a thumbnail image of the current drawing
    /// - Parameter size: Maximum size for the thumbnail
    /// - Returns: Thumbnail image
    func generateThumbnail(maxSize: CGSize) -> UIImage
    
    /// Exports the drawing as a full-resolution image
    /// - Returns: Full-resolution image of the drawing
    func exportImage() -> UIImage
}

/// Concrete implementation wrapping PencilKit
final class PencilKitDrawingEngine: DrawingEngineProtocol {
    // MARK: - Properties
    
    private weak var canvasView: PKCanvasView?
    
    var drawing: PKDrawing {
        get { canvasView?.drawing ?? PKDrawing() }
        set { canvasView?.drawing = newValue }
    }
    
    var currentTool: PKInkingTool {
        get { canvasView?.tool as? PKInkingTool ?? PKInkingTool(.pen, color: .black) }
        set { canvasView?.tool = newValue }
    }
    
    var hasUnsavedChanges: Bool {
        // Track changes via drawing modification count or custom flag
        return true  // Simplified for protocol example
    }
    
    // MARK: - Initialization
    
    init(canvasView: PKCanvasView) {
        self.canvasView = canvasView
    }
    
    // MARK: - Implementation
    
    // ... (implementation details in actual code)
}
```

**Contract Guarantees**:

- Drawing engine is independent of UI framework (can be SwiftUI or UIKit view)
- Undo/redo operations return success status for UI feedback
- Thumbnail generation is fast (scaled-down rendering)
- Tool selection is immediate (no async operations)

**Usage Example**:

```swift
// In View or ViewModel
class DrawingCanvasView: UIView {
    private let drawingEngine: DrawingEngineProtocol
    
    func handleColorSelection(color: UIColor) {
        drawingEngine.selectBrush(color: color, size: 10.0)
    }
    
    func handleUndoTapped() {
        if drawingEngine.undo() {
            // Show undo confirmation
        } else {
            // Show "nothing to undo" message
        }
    }
}
```

---

### 4. `ImageProcessor.swift` - Image Manipulation

**Purpose**: Handles image scaling, optimization, and validation for imported coloring pages

```swift
import UIKit

/// Protocol defining image processing operations
protocol ImageProcessorProtocol {
    /// Scales an image to fit within maximum dimensions while maintaining aspect ratio
    /// - Parameters:
    ///   - image: Source image
    ///   - maxSize: Maximum width/height
    /// - Returns: Scaled image
    func scaleImage(_ image: UIImage, toFit maxSize: CGSize) -> UIImage
    
    /// Optimizes an image for storage (compression, format conversion)
    /// - Parameters:
    ///   - image: Source image
    ///   - quality: JPEG quality (0.0-1.0) if converting to JPEG
    /// - Returns: Optimized image data
    func optimizeForStorage(_ image: UIImage, quality: CGFloat) -> Data?
    
    /// Validates an image is suitable for use as a coloring page
    /// - Parameter image: Image to validate
    /// - Returns: true if valid, false otherwise
    func validateColoringPageImage(_ image: UIImage) -> Bool
    
    /// Generates a thumbnail from an image
    /// - Parameters:
    ///   - image: Source image
    ///   - size: Desired thumbnail size
    /// - Returns: Thumbnail image
    func generateThumbnail(from image: UIImage, size: CGSize) -> UIImage
}

/// Concrete implementation using CoreGraphics
final class ImageProcessor: ImageProcessorProtocol {
    // MARK: - Configuration
    
    /// Maximum dimensions for coloring page images (iPad Pro 12.9" resolution)
    static let maxColoringPageSize = CGSize(width: 2732, height: 2048)
    
    /// Minimum dimensions to prevent low-quality images
    static let minColoringPageSize = CGSize(width: 512, height: 512)
    
    // MARK: - Implementation
    
    // ... (implementation details in actual code)
}
```

**Contract Guarantees**:

- Image scaling preserves aspect ratio
- Validation prevents unsuitable images (too small, corrupted, wrong format)
- Thumbnail generation is fast and consistent
- Storage optimization balances quality vs file size

---

## Dependency Injection Pattern

**Why**: Protocols enable dependency injection, making code testable and modular.

**Example**: ViewModels depend on protocols, not concrete classes

```swift
// ✅ Good: Depends on protocol (testable, flexible)
class ColoringPageGalleryViewModel: ObservableObject {
    private let repository: ColoringPageRepositoryProtocol
    
    init(repository: ColoringPageRepositoryProtocol) {
        self.repository = repository
    }
}

// ❌ Bad: Depends on concrete class (hard to test, tightly coupled)
class ColoringPageGalleryViewModel: ObservableObject {
    private let repository = ColoringPageRepository()  // Hard-coded dependency
}

// Production code uses real implementation:
let viewModel = ColoringPageGalleryViewModel(repository: ColoringPageRepository())

// Test code uses mock implementation:
let viewModel = ColoringPageGalleryViewModel(repository: MockColoringPageRepository())
```

---

## Mock Implementations for Testing

```swift
#if DEBUG

/// Mock repository for testing without file system access
class MockColoringPageRepository: ColoringPageRepositoryProtocol {
    var mockPages: [ColoringPage] = []
    var shouldThrowError = false
    
    func loadAll() async throws -> [ColoringPage] {
        if shouldThrowError { throw StorageError.fileNotFound("Mock error") }
        return mockPages
    }
    
    func load(id: UUID) async throws -> ColoringPage {
        if shouldThrowError { throw StorageError.fileNotFound("Mock error") }
        return mockPages.first(where: { $0.id == id }) 
            ?? ColoringPage.mock()
    }
    
    // ... other methods
}

/// Mock drawing engine for testing without PencilKit
class MockDrawingEngine: DrawingEngineProtocol {
    var drawing = PKDrawing()
    var currentTool = PKInkingTool(.pen, color: .black)
    var hasUnsavedChanges = false
    var undoStack: [PKDrawing] = []
    
    func selectBrush(color: UIColor, size: CGFloat) {
        currentTool = PKInkingTool(.pen, color: color, width: size)
    }
    
    func undo() -> Bool {
        guard !undoStack.isEmpty else { return false }
        drawing = undoStack.removeLast()
        return true
    }
    
    // ... other methods
}

#endif
```

---

## Contract Evolution Guidelines

**Adding new methods to protocols**:

1. **Non-breaking**: Add default implementation via protocol extension

```swift
extension ColoringPageRepositoryProtocol {
    /// New optional method with default implementation
    func exportColoringPage(id: UUID, to url: URL) async throws {
        // Default implementation
    }
}
```

2. **Breaking change**: Create new protocol version

```swift
protocol ColoringPageRepositoryV2Protocol: ColoringPageRepositoryProtocol {
    func newRequiredMethod() async throws
}
```

**Deprecating methods**:

```swift
protocol ColoringPageRepositoryProtocol {
    @available(*, deprecated, message: "Use loadAll() instead")
    func getAllPages() throws -> [ColoringPage]
}
```

---

## Integration Points

**UI Layer → Service Layer**:

```text
SwiftUI View
    └─> ViewModel (ObservableObject)
            └─> Repository (Protocol)
                    └─> FileManager / PencilKit
```

**Service Layer → Service Layer**:

```text
ArtworkRepository
    └─> ImageProcessor (generates thumbnails)
    └─> DrawingEngine (exports images)
```

**Testing Layer**:

```text
Unit Test
    └─> ViewModel (real)
            └─> MockRepository (fake)
                    └─> No file system access
```

---

## Summary

These contracts establish:

✅ **Loose coupling**: Components depend on abstractions (protocols) not implementations  
✅ **Testability**: Mock implementations enable fast, reliable unit tests  
✅ **Modularity**: Each protocol defines a focused responsibility  
✅ **Flexibility**: Can swap implementations (e.g., PencilKit → custom drawing engine) without changing consumers  
✅ **Clear boundaries**: Layers communicate through well-defined interfaces  
✅ **Type safety**: Swift's type system enforces contract adherence at compile time  

**Next Step**: Create quickstart.md with setup and deployment instructions.
