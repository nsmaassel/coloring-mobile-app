# Implementation Tasks: Interactive Coloring Pages

**Branch**: `001-coloring-pages` | **Generated**: 2025-11-09  
**Spec**: [spec.md](./spec.md) | **Plan**: [plan.md](./plan.md)

This document breaks down the feature into actionable implementation tasks organized by priority and dependency. Each task includes acceptance criteria, complexity estimates, and implementation notes.

---

## Phase P0: Project Setup

### Task P0.1: Create Xcode Project
**Priority**: P0 (blocking all development)  
**Complexity**: Simple (1-2 hours)  
**Dependencies**: None  
**Owner**: Developer (on Mac)

**Description**: Create the ColoringApp Xcode project with proper configuration for iPad development and Apple Pencil support.

**Acceptance Criteria**:
- [ ] Xcode project created with iOS App template
- [ ] Deployment target set to iOS 15.0
- [ ] Device families restricted to iPad only
- [ ] Project structure matches `plan.md` (feature-based modules)
- [ ] Git repository initialized and linked to remote
- [ ] Project builds successfully in Simulator

**Implementation Notes**:
- Follow `quickstart.md` Section 3 (Creating Your First iOS Project)
- Use SwiftUI App template
- Configure bundle identifier: `com.yourname.ColoringApp`
- Enable automatic code signing with free Apple ID
- Test build on iPad Simulator before proceeding

---

### Task P0.2: Add Built-in Coloring Pages
**Priority**: P0 (required for P1 testing)  
**Complexity**: Simple (30 min)  
**Dependencies**: P0.1  
**Owner**: Developer

**Description**: Add at least 3 simple line-art coloring page images to the project as bundled resources.

**Acceptance Criteria**:
- [ ] 3+ PNG images added to `ColoringApp/Resources/ColoringPages/`
- [ ] Images are black-and-white line art suitable for ages 3-8
- [ ] Images added to asset catalog with appropriate names
- [ ] Images accessible via Bundle at runtime
- [ ] Test images display correctly in Simulator

**Implementation Notes**:
- Use simple line art (animals, shapes, vehicles)
- Recommended size: 2048x2048 @2x for Retina displays
- Format: PNG with transparency
- Reference: `data-model.md` Section 1.1 (ColoringPage entity)

---

## Phase P1: Core Coloring Experience

### Task P1.1: ColoringPage Repository & Gallery View
**Priority**: P1 (User Story 1)  
**Complexity**: Medium (3-4 hours)  
**Dependencies**: P0.1, P0.2  
**Owner**: Developer

**Description**: Implement the coloring page gallery screen showing available templates. Users can browse and select pages.

**Acceptance Criteria**:
- [ ] `ColoringPageRepository` protocol implemented (see `contracts/README.md`)
- [ ] Gallery view displays thumbnails of all available coloring pages
- [ ] Tapping a thumbnail navigates to DrawingCanvas view
- [ ] Loading spinner shown during page load
- [ ] Gallery displays at app launch
- [ ] Test: Can launch app, see 3+ thumbnails, tap one, see it load

**Implementation Notes**:
- Use SwiftUI `ScrollView` with `LazyVGrid` for gallery layout
- Protocol: `ColoringPageRepositoryProtocol` from contracts
- Model: `ColoringPage` struct from `data-model.md`
- Load built-in pages from Bundle using `Bundle.main.url(forResource:withExtension:)`
- Thumbnail generation: Scale images to 300x300 for grid display
- Navigation: Use `NavigationStack` (iOS 16+) or `NavigationView`

**Files to Create**:
- `Features/ColoringPageGallery/Views/ColoringPageGalleryView.swift`
- `Features/ColoringPageGallery/ViewModels/ColoringPageGalleryViewModel.swift`
- `Core/Storage/ColoringPageRepository.swift`

---

### Task P1.2: Drawing Canvas with PencilKit
**Priority**: P1 (User Story 2 - foundation)  
**Complexity**: Medium (4-5 hours)  
**Dependencies**: P1.1  
**Owner**: Developer

**Description**: Implement the drawing canvas using PencilKit. Display selected coloring page as background, support Apple Pencil and touch drawing.

**Acceptance Criteria**:
- [ ] `PKCanvasView` integrated via `UIViewRepresentable`
- [ ] Selected coloring page displays as canvas background
- [ ] Can draw strokes with finger (touch input works)
- [ ] Can draw strokes with Apple Pencil (if available)
- [ ] Strokes appear immediately with minimal lag (<20ms target)
- [ ] Test: Load page, draw with finger, see strokes appear instantly

**Implementation Notes**:
- Use PencilKit's `PKCanvasView` (iOS 13+) wrapped in SwiftUI
- Set canvas background to coloring page image (scaled to fit)
- PencilKit handles Apple Pencil pressure/tilt automatically
- Configure `PKCanvasView.tool` with `PKInkingTool` for drawing
- Disable ruler tool (not needed for coloring)
- Reference: `research.md` Section 3 (PencilKit Specialist)

**Files to Create**:
- `Features/DrawingCanvas/Views/DrawingCanvasView.swift`
- `Features/DrawingCanvas/Views/PKCanvasViewRepresentable.swift`
- `Features/DrawingCanvas/ViewModels/DrawingCanvasViewModel.swift`

---

### Task P1.3: Basic Color Palette
**Priority**: P1 (User Story 2 - colors)  
**Complexity**: Simple (2-3 hours)  
**Dependencies**: P1.2  
**Owner**: Developer

**Description**: Add a color selection palette with 12+ vibrant colors. Selecting a color changes the active drawing color.

**Acceptance Criteria**:
- [ ] Palette displays 12+ distinct, vibrant colors
- [ ] Colors arranged in accessible grid (44x44pt touch targets minimum)
- [ ] Tapping a color changes active `PKInkingTool` color
- [ ] Selected color has clear visual indication (border/glow)
- [ ] Palette visible while drawing (overlay or side panel)
- [ ] Test: Select red, draw, select blue, draw, see different colored strokes

**Implementation Notes**:
- Define color palette in `Brush` model (see `data-model.md` Section 4)
- Use SwiftUI `HStack`/`VStack` or `LazyVGrid` for color buttons
- Update `PKCanvasView.tool` when color changes: `PKInkingTool(.pen, color: selectedColor)`
- Predefined colors: red, orange, yellow, green, blue, purple, pink, brown, black, white, gray, cyan
- Use iOS system colors where appropriate (`.red`, `.blue`, etc.)

**Files to Create**:
- `Features/BrushTools/Views/ColorPaletteView.swift`
- `Features/BrushTools/Models/Brush.swift`

---

### Task P1.4: Eraser Tool
**Priority**: P1 (User Story 3 - eraser)  
**Complexity**: Simple (1-2 hours)  
**Dependencies**: P1.2  
**Owner**: Developer

**Description**: Add an eraser tool that removes strokes when dragged over colored areas.

**Acceptance Criteria**:
- [ ] Eraser button visible in tool palette
- [ ] Tapping eraser activates eraser mode
- [ ] Active eraser has clear visual indication
- [ ] Dragging eraser removes existing strokes
- [ ] Switching back to color deactivates eraser
- [ ] Test: Draw strokes, activate eraser, erase part of drawing

**Implementation Notes**:
- PencilKit provides built-in eraser: `PKEraserTool(.vector)` or `.bitmap`
- Use vector eraser (removes entire strokes) for simplicity
- Toggle between `PKInkingTool` (color) and `PKEraserTool` (eraser)
- Update `PKCanvasView.tool` when eraser selected
- Visual indication: Highlight eraser button, change cursor (iOS may do this automatically)

**Files to Update**:
- `Features/BrushTools/Views/ColorPaletteView.swift` (add eraser button)
- `Features/DrawingCanvas/ViewModels/DrawingCanvasViewModel.swift` (tool switching logic)

---

### Task P1.5: Undo/Redo Functionality
**Priority**: P1 (User Story 3 - undo)  
**Complexity**: Simple (1 hour)  
**Dependencies**: P1.2  
**Owner**: Developer

**Description**: Add undo and redo buttons that reverse/restore drawing actions.

**Acceptance Criteria**:
- [ ] Undo button visible in UI
- [ ] Redo button visible in UI
- [ ] Tapping undo removes most recent stroke
- [ ] Tapping redo restores most recently undone stroke
- [ ] Buttons disabled when no actions available
- [ ] Test: Draw 3 strokes, undo 2, redo 1, verify correct stroke history

**Implementation Notes**:
- PencilKit provides built-in undo/redo via `PKCanvasView.undoManager`
- Wire undo/redo buttons to `canvasView.undoManager?.undo()` and `redo()`
- Disable buttons based on `undoManager?.canUndo` / `canRedo` state
- Use SF Symbols: `arrow.uturn.backward` (undo), `arrow.uturn.forward` (redo)

**Files to Update**:
- `Features/DrawingCanvas/Views/DrawingCanvasView.swift` (add undo/redo buttons)
- `Features/DrawingCanvas/ViewModels/DrawingCanvasViewModel.swift` (undo/redo actions)

---

## Phase P2: Persistence & Custom Content

### Task P2.1: Artwork Storage Repository
**Priority**: P2 (User Story 4 - foundation)  
**Complexity**: Medium (3-4 hours)  
**Dependencies**: P1.2  
**Owner**: Developer

**Description**: Implement file-based storage for saving and loading artwork. Uses FileManager and Codable for persistence.

**Acceptance Criteria**:
- [ ] `ArtworkRepository` protocol implemented (see `contracts/README.md`)
- [ ] Can save artwork with metadata and drawing data (PKDrawing)
- [ ] Can load saved artwork metadata (list of all saved artworks)
- [ ] Can load specific artwork drawing data (PKDrawing)
- [ ] File system structure matches `data-model.md` (Documents/Artworks/)
- [ ] Test: Save artwork, restart app, load artwork, verify strokes preserved

**Implementation Notes**:
- Protocol: `ArtworkRepositoryProtocol` from contracts
- Model: `Artwork` struct from `data-model.md`
- Storage location: `FileManager.default.urls(for: .documentDirectory, in: .userDomainMask)`
- Directory structure:
  ```
  Documents/
    Artworks/
      {uuid}/
        metadata.json    (Artwork struct as JSON via Codable)
        drawing.data     (PKDrawing serialized via PKDrawing.dataRepresentation())
        thumbnail.png    (optional, for gallery)
  ```
- Use async/await for file operations (avoid blocking main thread)

**Files to Create**:
- `Core/Storage/ArtworkRepository.swift`
- `Core/Storage/FileManagerExtensions.swift` (helper methods)
- `Core/Storage/StorageError.swift` (error types from `data-model.md`)

---

### Task P2.2: Save Artwork Functionality
**Priority**: P2 (User Story 4 - save)  
**Complexity**: Simple (2 hours)  
**Dependencies**: P2.1  
**Owner**: Developer

**Description**: Add a save button to the drawing canvas. Saves current drawing and metadata to persistent storage.

**Acceptance Criteria**:
- [ ] Save button visible in drawing canvas UI
- [ ] Tapping save persists current PKDrawing to disk
- [ ] Save includes metadata (timestamp, coloring page reference)
- [ ] Success confirmation shown (alert or toast)
- [ ] Test: Color a page, tap save, verify file created in Documents directory

**Implementation Notes**:
- Save button in toolbar (SF Symbol: `square.and.arrow.down`)
- Call `artworkRepository.save(artwork:drawing:)` from ViewModel
- Generate thumbnail using `PKCanvasView.drawing.image(from:scale:)`
- Show SwiftUI alert on success: "Artwork saved!"
- Handle errors gracefully (show error message if save fails)

**Files to Update**:
- `Features/DrawingCanvas/Views/DrawingCanvasView.swift` (add save button)
- `Features/DrawingCanvas/ViewModels/DrawingCanvasViewModel.swift` (save action)

---

### Task P2.3: Artwork Gallery View
**Priority**: P2 (User Story 4 - gallery)  
**Complexity**: Medium (3 hours)  
**Dependencies**: P2.1, P2.2  
**Owner**: Developer

**Description**: Create a gallery view showing all saved artwork. Users can browse and reopen saved art for editing.

**Acceptance Criteria**:
- [ ] Gallery view displays thumbnails of all saved artwork
- [ ] Thumbnails show preview of colored page
- [ ] Tapping thumbnail opens artwork in DrawingCanvas for editing
- [ ] Gallery accessible from main navigation
- [ ] Empty state shown when no saved artwork exists
- [ ] Test: Save 3 artworks, navigate to gallery, see all 3, tap one, see it load

**Implementation Notes**:
- Similar layout to ColoringPageGallery (reuse grid pattern)
- Use `ArtworkRepository.loadAllMetadata()` to get list
- Display thumbnails from saved thumbnail.png files
- Navigation: `NavigationLink` to DrawingCanvasView with artwork ID
- Empty state: "No saved artwork yet. Start coloring to save your first masterpiece!"

**Files to Create**:
- `Features/ArtworkGallery/Views/ArtworkGalleryView.swift`
- `Features/ArtworkGallery/ViewModels/ArtworkGalleryViewModel.swift`

---

### Task P2.4: Reopen Artwork for Editing
**Priority**: P2 (User Story 4 - reopen)  
**Complexity**: Simple (1-2 hours)  
**Dependencies**: P2.3  
**Owner**: Developer

**Description**: Enable opening saved artwork from gallery back into the drawing canvas with all strokes preserved.

**Acceptance Criteria**:
- [ ] Tapping artwork in gallery loads it in DrawingCanvas
- [ ] All previous strokes restored exactly as saved
- [ ] Can continue coloring (add new strokes)
- [ ] Can save changes (updates existing artwork file)
- [ ] Test: Save artwork, reopen, add more strokes, save again, verify changes persist

**Implementation Notes**:
- Pass artwork ID to DrawingCanvasView via NavigationLink
- In ViewModel `onAppear`, check if artwork ID exists:
  - If yes: Load `PKDrawing` via `artworkRepository.loadDrawing(artworkId:)`
  - Set `PKCanvasView.drawing = loadedDrawing`
- Save operation updates existing file instead of creating new one

**Files to Update**:
- `Features/DrawingCanvas/Views/DrawingCanvasView.swift` (accept artwork ID parameter)
- `Features/DrawingCanvas/ViewModels/DrawingCanvasViewModel.swift` (load existing artwork logic)

---

### Task P2.5: Import Custom Coloring Pages
**Priority**: P2 (User Story 5)  
**Complexity**: Medium (3-4 hours)  
**Dependencies**: P1.1  
**Owner**: Developer

**Description**: Add ability to import custom coloring page images from device photo library using iOS photo picker.

**Acceptance Criteria**:
- [ ] "Add Coloring Page" button visible in gallery
- [ ] Tapping button opens PHPickerViewController (iOS 14+ photo picker)
- [ ] Selecting an image imports it as a custom coloring page
- [ ] Custom page appears in gallery alongside built-in pages
- [ ] Custom page usable for coloring (same as built-in)
- [ ] Test: Tap add, select photo, see it in gallery, color it

**Implementation Notes**:
- Use `PHPickerViewController` for privacy-first photo picking
- Wrap PHPickerViewController in `UIViewControllerRepresentable` for SwiftUI
- Filter to images only: `PHPickerConfiguration.filter = .images`
- Save imported image to Documents/ColoringPages/ directory
- Generate thumbnail and save to asset catalog or file system
- Update `ColoringPageRepository` to include custom pages in `loadAll()`

**Files to Create**:
- `Features/ColoringPageGallery/Views/PhotoPickerView.swift` (UIViewControllerRepresentable wrapper)
- `Core/Storage/ImageProcessor.swift` (scale/optimize imported images)

**Files to Update**:
- `Features/ColoringPageGallery/Views/ColoringPageGalleryView.swift` (add import button)
- `Core/Storage/ColoringPageRepository.swift` (support custom pages)

---

## Phase P3: Polish & Enhancements

### Task P3.1: Special Brush Effects (Rainbow)
**Priority**: P3 (User Story 6 - rainbow)  
**Complexity**: Medium (3-4 hours)  
**Dependencies**: P1.3  
**Owner**: Developer

**Description**: Add a rainbow brush that creates multi-color gradient strokes.

**Acceptance Criteria**:
- [ ] Rainbow brush option visible in brush palette
- [ ] Selecting rainbow brush enables gradient effect
- [ ] Drawing with rainbow brush creates colorful gradient strokes
- [ ] Effect performs smoothly at 60fps
- [ ] Test: Select rainbow, draw curvy line, see smooth color transitions

**Implementation Notes**:
- PencilKit doesn't support multi-color strokes natively
- Options:
  1. Use custom `PKInkingTool` with Core Graphics gradient (complex)
  2. Cycle through colors programmatically as user draws (simpler)
  3. Post-process strokes to apply gradient shader (advanced)
- Recommended: Start with option 2 (cycle colors) for learning
- Change ink color every N points along stroke path
- Document limitations in user-facing UI ("simplified rainbow effect")

**Files to Update**:
- `Features/BrushTools/Views/ColorPaletteView.swift` (add rainbow button)
- `Features/BrushTools/Models/Brush.swift` (add BrushType.rainbow enum case)
- `Features/DrawingCanvas/ViewModels/DrawingCanvasViewModel.swift` (rainbow logic)

---

### Task P3.2: Special Brush Effects (Sparkle)
**Priority**: P3 (User Story 6 - sparkle)  
**Complexity**: Hard (5-6 hours)  
**Dependencies**: P1.3  
**Owner**: Developer

**Description**: Add a sparkle brush that creates glittery/shimmering visual effects on strokes.

**Acceptance Criteria**:
- [ ] Sparkle brush option visible in brush palette
- [ ] Selecting sparkle brush enables sparkle effect
- [ ] Drawing with sparkle brush creates glittery appearance
- [ ] Effect performs smoothly (no lag/stuttering)
- [ ] Test: Select sparkle, draw, see sparkle particles or shimmer

**Implementation Notes**:
- This is significantly harder than rainbow—requires custom rendering
- Options:
  1. Particle system overlay (CAEmitterLayer) following stroke path
  2. Custom texture on `PKInkingTool` (limited control)
  3. Post-process with Core Image filters (shimmering effect)
- Recommended: Use CAEmitterLayer with star/dot particles
- Attach emitter layer to PKCanvasView, update position as strokes drawn
- Performance critical: Limit particle count, use simple shapes
- Consider deferring this to future iteration if too complex

**Files to Update**:
- `Features/BrushTools/Views/ColorPaletteView.swift` (add sparkle button)
- `Features/BrushTools/Models/Brush.swift` (add BrushType.sparkle enum case)
- `Features/DrawingCanvas/ViewModels/DrawingCanvasViewModel.swift` (sparkle logic)
- Possibly: `Features/DrawingCanvas/Views/SparkleEffectView.swift` (custom overlay)

---

### Task P3.3: Start Fresh / New Page Navigation
**Priority**: P3 (User Story 7)  
**Complexity**: Simple (1-2 hours)  
**Dependencies**: P1.1, P1.2  
**Owner**: Developer

**Description**: Add navigation to return to gallery and start a new coloring page. Prompt to save unsaved work.

**Acceptance Criteria**:
- [ ] "New Page" button visible in drawing canvas UI
- [ ] Tapping button returns to coloring page gallery
- [ ] If unsaved changes exist, prompt user: "Save before leaving?"
- [ ] User can choose: Save, Discard, Cancel
- [ ] Test: Color a page (don't save), tap new page, see save prompt, choose save, verify artwork saved

**Implementation Notes**:
- Use SwiftUI navigation: `@Environment(\.dismiss)` to pop back to gallery
- Track unsaved changes: Compare current `PKDrawing` hash with last saved version
- Show `Alert` with 3 buttons: "Save", "Discard", "Cancel"
- "Save" button: Call save action, then dismiss
- "Discard" button: Dismiss without saving
- "Cancel" button: Stay on canvas

**Files to Update**:
- `Features/DrawingCanvas/Views/DrawingCanvasView.swift` (add "New Page" button, save prompt)
- `Features/DrawingCanvas/ViewModels/DrawingCanvasViewModel.swift` (unsaved changes tracking)

---

## Testing & Quality Assurance

### Task QA.1: Unit Tests for Repositories
**Priority**: P2 (testing for core storage)  
**Complexity**: Medium (3-4 hours)  
**Dependencies**: P2.1  
**Owner**: Developer

**Description**: Write unit tests for ColoringPageRepository and ArtworkRepository using XCTest.

**Acceptance Criteria**:
- [ ] Test ColoringPageRepository.loadAll() returns built-in pages
- [ ] Test ColoringPageRepository.importCustomPage() saves image correctly
- [ ] Test ArtworkRepository.save() creates files in correct directory structure
- [ ] Test ArtworkRepository.loadMetadata() returns saved artwork metadata
- [ ] Test error handling (invalid image, storage full, corrupted files)
- [ ] All tests pass in Xcode Test Navigator

**Implementation Notes**:
- Use XCTest framework (built into Xcode)
- Create mock file system using temporary directory for tests
- Reference: `contracts/README.md` Section 5 (Mock Implementations)
- Test data: Use small test images from Resources/TestAssets/
- Clean up test files in `tearDown()` method

**Files to Create**:
- `ColoringAppTests/Core/ColoringPageRepositoryTests.swift`
- `ColoringAppTests/Core/ArtworkRepositoryTests.swift`
- `ColoringAppTests/Mocks/MockFileManager.swift`

---

### Task QA.2: UI Tests for Critical User Flows
**Priority**: P3 (end-to-end validation)  
**Complexity**: Medium (3-4 hours)  
**Dependencies**: P1.1, P1.2, P2.2  
**Owner**: Developer

**Description**: Write UI automation tests for User Stories 1-4 using XCUITest.

**Acceptance Criteria**:
- [ ] Test: Launch app → see gallery → tap page → page loads in canvas
- [ ] Test: Load page → select color → draw stroke → stroke appears
- [ ] Test: Draw strokes → tap eraser → erase → strokes removed
- [ ] Test: Draw → tap undo → last stroke removed
- [ ] Test: Draw → tap save → success message → gallery shows saved artwork
- [ ] All tests pass on iPad Simulator

**Implementation Notes**:
- Use XCUITest framework (UI automation for iOS)
- Access UI elements via accessibility identifiers (add to SwiftUI views)
- Run tests in iPad Simulator (UI tests require simulator or device)
- Reference: Apple's XCUITest documentation
- Keep tests simple for learning—focus on happy paths

**Files to Create**:
- `ColoringAppUITests/ColoringFlowUITests.swift`

---

## Deployment & Documentation

### Task D.1: TestFlight Beta Deployment
**Priority**: Optional (after P1 complete)  
**Complexity**: Medium (2-3 hours)  
**Dependencies**: P1.5  
**Owner**: Developer

**Description**: Deploy the app to TestFlight for household testing on family iPads.

**Acceptance Criteria**:
- [ ] App successfully archived in Xcode
- [ ] App uploaded to App Store Connect
- [ ] TestFlight build available for internal testing
- [ ] Family members can install via TestFlight app
- [ ] Test: Install on 2+ household iPads, verify basic functionality

**Implementation Notes**:
- Follow `quickstart.md` Section 9 (TestFlight & App Store)
- Requires paid Apple Developer account ($99/year) OR
- Alternative: Use free sideloading (7-day expiration) per `quickstart.md` Section 7
- TestFlight internal testing: No review required, instant deployment
- Add family members as internal testers in App Store Connect

---

### Task D.2: User Documentation
**Priority**: P3 (helpful for family use)  
**Complexity**: Simple (1-2 hours)  
**Dependencies**: P3.3 (all features complete)  
**Owner**: Developer

**Description**: Create simple user guide for children and parents.

**Acceptance Criteria**:
- [ ] README.md in repository with "How to Use" section
- [ ] Document key features: selecting pages, coloring, saving, importing
- [ ] Include screenshots of main screens
- [ ] Troubleshooting section for common issues
- [ ] Age-appropriate language (simple, clear instructions)

**Implementation Notes**:
- Add to repository root: `docs/USER_GUIDE.md`
- Use screenshots from iPad Simulator or actual device
- Cover all P1/P2 features, note P3 as optional enhancements
- Parent section: How to import custom coloring pages
- Child section: How to color, save, and start new pages

**Files to Create**:
- `docs/USER_GUIDE.md`

---

## Summary

**Total Tasks**: 21 (6 P0, 5 P1, 5 P2, 3 P3, 2 QA, 2 Deployment)

**Estimated Timeline** (solo developer, part-time):
- **P0 (Setup)**: 1-2 days
- **P1 (Core Coloring)**: 1-2 weeks
- **P2 (Persistence & Import)**: 1 week
- **P3 (Polish)**: 1 week
- **QA & Deployment**: 3-5 days

**Total**: ~4-6 weeks of part-time work (10-15 hours/week)

**Critical Path**: P0 → P1.1 → P1.2 → P1.3 → P1.4 → P1.5 (Core coloring experience)

**Suggested Development Order**:
1. Complete all P0 tasks (project setup)
2. Complete P1 tasks in sequence (1.1 → 1.2 → 1.3 → 1.4 → 1.5)
3. Demo core coloring to family, gather feedback
4. Complete P2 tasks (persistence and import)
5. Optional: Complete P3 tasks (special effects and polish)
6. Write tests (QA.1, QA.2)
7. Deploy to TestFlight or sideload to family iPads

**Next Steps**: Begin with Task P0.1 (Create Xcode Project) on MacBook Pro following `quickstart.md`.
