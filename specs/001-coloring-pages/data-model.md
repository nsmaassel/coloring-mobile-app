# Data Model: Interactive Coloring Pages

**Feature**: 001-coloring-pages  
**Date**: 2025-11-09  
**Purpose**: Define data structures, persistence patterns, and entity relationships for the coloring app

---

## Overview

This document defines the Swift data models that represent the core domain entities identified in the feature specification. All models use Swift's `Codable` protocol for JSON serialization to files, as determined in research.md.

---

## Entity Definitions

### 1. ColoringPage

Represents a coloring page template (either built-in or custom-imported) that serves as the base image for coloring activities.

**Swift Model**:

```swift
import Foundation
import UIKit

struct ColoringPage: Codable, Identifiable {
    // MARK: - Properties
    
    /// Unique identifier for this coloring page
    let id: UUID
    
    /// Display name for the coloring page (e.g., "Butterfly Garden", "Space Adventure")
    var name: String
    
    /// Filename of the image in the coloring-pages/ directory (e.g., "page-001.png")
    let imageFilename: String
    
    /// Indicates if this is a built-in page (bundled with app) or custom (user-imported)
    let isBuiltIn: Bool
    
    /// Date when the page was added to the library (import date for custom pages)
    let dateAdded: Date
    
    /// Optional category/tag for organization (e.g., "Animals", "Nature", "Abstract")
    var category: String?
    
    // MARK: - Initialization
    
    init(id: UUID = UUID(), 
         name: String, 
         imageFilename: String, 
         isBuiltIn: Bool, 
         dateAdded: Date = Date(),
         category: String? = nil) {
        self.id = id
        self.name = name
        self.imageFilename = imageFilename
        self.isBuiltIn = isBuiltIn
        self.dateAdded = dateAdded
        self.category = category
    }
    
    // MARK: - Computed Properties
    
    /// Full file URL for the coloring page image
    /// - Returns: URL in Documents/coloring-pages/ for custom pages, Bundle for built-in
    func imageURL(documentDirectory: URL) -> URL {
        if isBuiltIn {
            // Built-in pages are in app bundle's Resources/ColoringPages/
            guard let bundleURL = Bundle.main.url(forResource: imageFilename.replacingOccurrences(of: ".png", with: ""), 
                                                   withExtension: "png",
                                                   subdirectory: "ColoringPages") else {
                fatalError("Built-in coloring page not found: \(imageFilename)")
            }
            return bundleURL
        } else {
            // Custom pages are in Documents/coloring-pages/
            return documentDirectory
                .appendingPathComponent("coloring-pages")
                .appendingPathComponent(imageFilename)
        }
    }
}

// MARK: - Sample Data for Previews

#if DEBUG
extension ColoringPage {
    static let sampleBuiltIn = ColoringPage(
        name: "Butterfly Garden",
        imageFilename: "butterfly-garden.png",
        isBuiltIn: true,
        category: "Nature"
    )
    
    static let sampleCustom = ColoringPage(
        name: "Family Photo Outline",
        imageFilename: "custom-001.png",
        isBuiltIn: false,
        dateAdded: Date(),
        category: "Custom"
    )
}
#endif
```

**Persistence**:

- **Metadata**: All `ColoringPage` instances stored in `Documents/coloring-pages/metadata.json` as JSON array
- **Images**: Image files stored in `Documents/coloring-pages/` (custom) or `Bundle.main/Resources/ColoringPages/` (built-in)

**Validation Rules**:

- `name` must not be empty
- `imageFilename` must be valid filename with supported extension (.png, .jpg, .jpeg)
- Built-in pages must exist in app bundle

---

### 2. Artwork

Represents a saved coloring creation, including all drawing strokes and metadata about when it was created and which coloring page it's based on.

**Swift Model**:

```swift
import Foundation
import PencilKit

struct Artwork: Codable, Identifiable {
    // MARK: - Properties
    
    /// Unique identifier for this artwork
    let id: UUID
    
    /// User-provided title (optional, defaults to "Untitled")
    var title: String
    
    /// Reference to the source coloring page this artwork is based on
    let sourceColoringPageID: UUID
    
    /// Date when the artwork was first created
    let createdDate: Date
    
    /// Date when the artwork was last modified (updated on each save)
    var modifiedDate: Date
    
    /// Filename for the PKDrawing data file (e.g., "drawing.pkdrawing")
    let drawingFilename: String
    
    /// Filename for the cached thumbnail image (e.g., "thumbnail.png")
    let thumbnailFilename: String
    
    /// Number of strokes in the drawing (cached for performance, updated on save)
    var strokeCount: Int
    
    // MARK: - Initialization
    
    init(id: UUID = UUID(),
         title: String = "Untitled",
         sourceColoringPageID: UUID,
         createdDate: Date = Date(),
         modifiedDate: Date = Date(),
         drawingFilename: String = "drawing.pkdrawing",
         thumbnailFilename: String = "thumbnail.png",
         strokeCount: Int = 0) {
        self.id = id
        self.title = title
        self.sourceColoringPageID = sourceColoringPageID
        self.createdDate = createdDate
        self.modifiedDate = modifiedDate
        self.drawingFilename = drawingFilename
        self.thumbnailFilename = thumbnailFilename
        self.strokeCount = strokeCount
    }
    
    // MARK: - Computed Properties
    
    /// Directory URL for this artwork's files
    func artworkDirectory(documentDirectory: URL) -> URL {
        return documentDirectory
            .appendingPathComponent("saved-artwork")
            .appendingPathComponent(id.uuidString)
    }
    
    /// Full file URL for the PKDrawing data
    func drawingURL(documentDirectory: URL) -> URL {
        return artworkDirectory(documentDirectory: documentDirectory)
            .appendingPathComponent(drawingFilename)
    }
    
    /// Full file URL for the thumbnail image
    func thumbnailURL(documentDirectory: URL) -> URL {
        return artworkDirectory(documentDirectory: documentDirectory)
            .appendingPathComponent(thumbnailFilename)
    }
    
    /// Full file URL for the metadata JSON
    func metadataURL(documentDirectory: URL) -> URL {
        return artworkDirectory(documentDirectory: documentDirectory)
            .appendingPathComponent("metadata.json")
    }
}

// MARK: - Sample Data for Previews

#if DEBUG
extension Artwork {
    static let sample = Artwork(
        title: "My Butterfly",
        sourceColoringPageID: ColoringPage.sampleBuiltIn.id,
        createdDate: Date().addingTimeInterval(-86400), // 1 day ago
        modifiedDate: Date(),
        strokeCount: 127
    )
}
#endif
```

**Persistence**:

- **Metadata**: Each artwork has its own `metadata.json` in `Documents/saved-artwork/{artwork-id}/`
- **Drawing data**: PencilKit's `PKDrawing` saved as `drawing.pkdrawing` using `PKDrawing.dataRepresentation()` (Codable)
- **Thumbnail**: Rendered thumbnail image saved as `thumbnail.png`

**Validation Rules**:

- `title` can be empty (defaults to "Untitled")
- `sourceColoringPageID` must reference an existing ColoringPage
- `modifiedDate` must be >= `createdDate`

---

### 3. DrawingStroke (implicit via PencilKit)

**Note**: PencilKit's `PKDrawing` class manages strokes internally via `PKStroke` objects. We don't need to define our own stroke model for basic functionality, but understanding the structure is valuable.

**PencilKit Structure** (for reference, not implemented):

```swift
// PencilKit provides these classes:

// PKStroke: A single drawing stroke
// - path: PKStrokePath (the path coordinates and points)
// - ink: PKInk (color and style)
// - transform: CGAffineTransform (position/scale/rotation)

// PKInk: Defines the appearance of a stroke
// - inkType: PKInkingTool.InkType (pen, pencil, marker, etc.)
// - color: UIColor
// - width: CGFloat (optional, defaults to tool width)

// PKStrokePath: The path of a stroke
// - count: Int (number of path points)
// - interpolatedPoints(in:by:): [PKStrokePoint] (get points along path)

// PKStrokePoint: A single point in a stroke path
// - location: CGPoint
// - timeOffset: TimeInterval
// - size: CGSize (based on pressure)
// - force: CGFloat (pressure from Apple Pencil)
// - azimuth: CGFloat (angle of pencil relative to surface)
// - altitude: CGFloat (tilt of pencil)
```

**Custom Stroke Model** (for future special brushes, if needed):

If PencilKit's built-in inks are insufficient for sparkle/rainbow effects (P3 features), we may need:

```swift
/// Custom stroke data for special effects not supported by PencilKit
struct CustomStroke: Codable {
    let id: UUID
    let brushType: BrushType
    let path: [CGPoint]  // Simplified path (can interpolate for rendering)
    let color: CodableColor  // Custom wrapper for UIColor
    let timestamp: Date
    let pressurePoints: [CGFloat]?  // Optional pressure data
}

/// Custom brush types for special effects
enum BrushType: String, Codable {
    case solid        // Standard PencilKit ink
    case rainbow      // Multi-color gradient along stroke path
    case sparkle      // Glittering particle effect
    case glow         // Soft glow around stroke
}
```

**Decision**: Use PencilKit's `PKStroke` for P1/P2 features. Research custom rendering for P3 special brushes in later phase.

---

### 4. Brush

Represents a drawing tool configuration (color, effect type, properties). For P1, this is primarily for UI state management, not persistence.

**Swift Model**:

```swift
import UIKit

/// Represents a brush/tool configuration for drawing
struct Brush: Equatable, Identifiable {
    let id: UUID
    let type: BrushType
    let color: UIColor
    let size: CGFloat  // Default stroke width
    
    init(id: UUID = UUID(),
         type: BrushType = .solid,
         color: UIColor,
         size: CGFloat = 10.0) {
        self.id = id
        self.type = type
        self.color = color
        self.size = size
    }
    
    // MARK: - Equatable
    
    static func == (lhs: Brush, rhs: Brush) -> Bool {
        return lhs.id == rhs.id &&
               lhs.type == rhs.type &&
               lhs.color == rhs.color &&
               lhs.size == rhs.size
    }
}

/// Brush type/effect
enum BrushType: String, CaseIterable {
    case solid        // Standard solid color brush (P1)
    case eraser       // Eraser tool (P1)
    case rainbow      // Rainbow gradient effect (P3)
    case sparkle      // Sparkle/glitter effect (P3)
    
    var displayName: String {
        switch self {
        case .solid: return "Brush"
        case .eraser: return "Eraser"
        case .rainbow: return "Rainbow"
        case .sparkle: return "Sparkle"
        }
    }
    
    var iconSystemName: String {
        switch self {
        case .solid: return "paintbrush.fill"
        case .eraser: return "eraser.fill"
        case .rainbow: return "paintpalette.fill"
        case .sparkle: return "sparkles"
        }
    }
}

// MARK: - Predefined Brushes

extension Brush {
    /// Standard color palette for children (12 distinct colors per spec)
    static let standardPalette: [Brush] = [
        // Primary colors
        Brush(color: .systemRed),
        Brush(color: .systemOrange),
        Brush(color: .systemYellow),
        Brush(color: .systemGreen),
        Brush(color: .systemBlue),
        Brush(color: .systemPurple),
        
        // Secondary/accent colors
        Brush(color: .systemPink),
        Brush(color: .systemBrown),
        Brush(color: .black),
        Brush(color: .white),
        Brush(color: .systemGray),
        Brush(color: .systemCyan)
    ]
    
    /// Eraser tool
    static let eraser = Brush(type: .eraser, color: .clear)
}
```

**Persistence**: Brush selection is **not persisted**—it's runtime UI state only. Default brush resets to first color on app launch.

**Validation Rules**:

- `size` must be > 0
- `color` can be any valid UIColor (including .clear for eraser)

---

## Relationships

```text
ColoringPage (1) ──< (many) Artwork
    │
    └─ One coloring page can be the source for many artworks
    └─ Each artwork references exactly one source coloring page
    
Artwork (1) ──── (1) PKDrawing
    │
    └─ Each artwork has one PKDrawing (contains all strokes)
    └─ PKDrawing contains many PKStroke objects (managed internally)

Brush ──X── (not persisted)
    │
    └─ Runtime state only, used to configure PencilKit's PKInkingTool
```

---

## File System Layout

```text
App Bundle (read-only):
└── Resources/
    └── ColoringPages/
        ├── butterfly-garden.png      # Built-in coloring page 1
        ├── space-adventure.png       # Built-in coloring page 2
        └── underwater-scene.png      # Built-in coloring page 3

Documents Directory (read-write):
├── coloring-pages/
│   ├── metadata.json                 # Array of all ColoringPage (custom + built-in references)
│   ├── custom-001.png                # User-imported coloring page 1
│   └── custom-002.jpg                # User-imported coloring page 2
│
└── saved-artwork/
    ├── {artwork-uuid-1}/
    │   ├── metadata.json             # Artwork metadata (Codable)
    │   ├── drawing.pkdrawing         # PKDrawing data (binary, Codable)
    │   └── thumbnail.png             # Cached thumbnail (UIImage)
    │
    └── {artwork-uuid-2}/
        ├── metadata.json
        ├── drawing.pkdrawing
        └── thumbnail.png
```

---

## Data Flow Patterns

### Loading a Coloring Page for Drawing

```swift
// 1. Load ColoringPage metadata from metadata.json
let coloringPage = try ColoringPageRepository.load(id: pageID)

// 2. Load the image from file system
let imageURL = coloringPage.imageURL(documentDirectory: documentsDirectory)
let image = UIImage(contentsOfFile: imageURL.path)

// 3. Display in canvas view
canvasView.backgroundImage = image
```

### Saving Artwork

```swift
// 1. Get PKDrawing from PencilKit canvas
let drawing = canvasView.drawing

// 2. Create Artwork metadata
var artwork = Artwork(
    title: userTitle,
    sourceColoringPageID: currentColoringPage.id,
    strokeCount: drawing.strokes.count
)

// 3. Generate thumbnail
let thumbnailImage = drawing.image(from: drawing.bounds, scale: 0.1)

// 4. Save to file system
try ArtworkRepository.save(
    artwork: artwork,
    drawing: drawing,
    thumbnail: thumbnailImage,
    to: documentsDirectory
)
```

### Loading Saved Artwork for Editing

```swift
// 1. Load Artwork metadata
let artwork = try ArtworkRepository.loadMetadata(id: artworkID)

// 2. Load PKDrawing data
let drawingURL = artwork.drawingURL(documentDirectory: documentsDirectory)
let drawingData = try Data(contentsOf: drawingURL)
let drawing = try PKDrawing(data: drawingData)

// 3. Load source coloring page
let coloringPage = try ColoringPageRepository.load(id: artwork.sourceColoringPageID)
let backgroundImage = UIImage(contentsOfFile: coloringPage.imageURL(documentDirectory: documentsDirectory).path)

// 4. Restore in canvas
canvasView.drawing = drawing
canvasView.backgroundImage = backgroundImage
```

---

## Performance Considerations

### Memory Management

- **Lazy loading**: Don't load all PKDrawing data into memory at once. Load only when artwork is opened for editing.
- **Thumbnail caching**: Use thumbnails for gallery display, not full-resolution drawings.
- **Image scaling**: Scale down high-resolution imported coloring pages to iPad display resolution (2732×2048 for 12.9" iPad Pro) to avoid excessive memory use.

### Codable Performance

- **PKDrawing serialization**: `PKDrawing.dataRepresentation()` is fast and compact. Typical drawing with 500 strokes ~50-200KB.
- **JSON metadata**: Artwork metadata is tiny (~1KB), negligible performance impact.
- **Batch operations**: When loading gallery, load metadata only first (not full drawings), then load thumbnails on-demand.

### Concurrency

- **Background file I/O**: All save operations should use `DispatchQueue.global()` to avoid blocking main thread.
- **Actor-based repositories** (Swift 5.5+): Consider using `actor` for repository classes to ensure thread-safe file operations.

```swift
actor ArtworkRepository {
    func save(artwork: Artwork, drawing: PKDrawing, thumbnail: UIImage) async throws {
        // File operations here are automatically serialized by actor
        // No risk of concurrent writes to same file
    }
}
```

---

## Validation & Error Handling

### File System Errors

```swift
enum StorageError: Error, LocalizedError {
    case fileNotFound(String)
    case corruptedData(String)
    case insufficientSpace
    case permissionDenied
    case invalidColoringPage(String)
    
    var errorDescription: String? {
        switch self {
        case .fileNotFound(let filename):
            return "Could not find file: \(filename)"
        case .corruptedData(let context):
            return "Data is corrupted: \(context)"
        case .insufficientSpace:
            return "Not enough storage space available"
        case .permissionDenied:
            return "Permission denied to access file"
        case .invalidColoringPage(let reason):
            return "Invalid coloring page: \(reason)"
        }
    }
}
```

### Data Validation

```swift
extension ColoringPage {
    /// Validates the coloring page data
    func validate() throws {
        guard !name.isEmpty else {
            throw StorageError.invalidColoringPage("Name cannot be empty")
        }
        
        let validExtensions = ["png", "jpg", "jpeg"]
        let ext = (imageFilename as NSString).pathExtension.lowercased()
        guard validExtensions.contains(ext) else {
            throw StorageError.invalidColoringPage("Invalid image format: \(ext)")
        }
    }
}

extension Artwork {
    /// Validates the artwork data
    func validate() throws {
        guard modifiedDate >= createdDate else {
            throw StorageError.corruptedData("Modified date before created date")
        }
        
        guard strokeCount >= 0 else {
            throw StorageError.corruptedData("Negative stroke count")
        }
    }
}
```

---

## Migration Strategy

**Version 1.0 (Initial Release)**:

- Establish baseline data model
- No migration needed

**Future Versions**:

If data model changes are needed (e.g., adding new fields to ColoringPage):

1. Add version number to metadata JSON:

```swift
struct ColoringPageMetadata: Codable {
    let version: Int = 1  // Increment when model changes
    let pages: [ColoringPage]
}
```

2. Implement migration logic in repository:

```swift
func loadMetadata() throws -> [ColoringPage] {
    let data = try Data(contentsOf: metadataURL)
    let metadata = try JSONDecoder().decode(ColoringPageMetadata.self, from: data)
    
    if metadata.version < 2 {
        return try migrateV1toV2(metadata.pages)
    }
    
    return metadata.pages
}
```

3. Never delete old data—always migrate forward

---

## Testing Considerations

### Unit Test Targets

**ColoringPage**:

- ✅ Codable serialization/deserialization
- ✅ imageURL() returns correct path for built-in vs custom
- ✅ validate() catches invalid data

**Artwork**:

- ✅ Codable serialization/deserialization
- ✅ artworkDirectory() returns correct path structure
- ✅ validate() catches invalid dates and stroke counts

**Brush**:

- ✅ Equatable comparison works correctly
- ✅ Standard palette has 12 colors
- ✅ BrushType enum cases map to correct display names

### Integration Test Targets

- ✅ Save artwork → reload artwork → verify drawing data matches
- ✅ Import coloring page → verify file copied and metadata updated
- ✅ Delete artwork → verify all files removed
- ✅ App launch → verify all metadata loaded correctly

### Mock Data for Tests

```swift
extension ColoringPage {
    static func mock(name: String = "Test Page", isBuiltIn: Bool = false) -> ColoringPage {
        return ColoringPage(
            name: name,
            imageFilename: "test-page.png",
            isBuiltIn: isBuiltIn,
            dateAdded: Date()
        )
    }
}

extension Artwork {
    static func mock(title: String = "Test Artwork") -> Artwork {
        return Artwork(
            title: title,
            sourceColoringPageID: UUID(),
            strokeCount: 42
        )
    }
}
```

---

## Summary

This data model establishes:

✅ **Four core entities**: ColoringPage, Artwork, DrawingStroke (via PencilKit), Brush  
✅ **File-based persistence**: JSON metadata + binary PKDrawing data  
✅ **Clear relationships**: ColoringPage → Artwork → PKDrawing  
✅ **Validation rules**: Type safety with Swift's strong typing + explicit validation methods  
✅ **Performance patterns**: Lazy loading, thumbnail caching, async file I/O  
✅ **Error handling**: Custom StorageError enum with descriptive messages  
✅ **Future extensibility**: Version-based migration strategy  

**Next Steps**: Define protocol contracts for storage repositories and drawing engine (Phase 1: contracts/).
