# coloring-mobile-app Development Guidelines

Auto-generated from all feature plans. Last updated: 2025-11-09

## Active Technologies

- Swift 5.9+ (latest stable, bundled with Xcode 15+)
- SwiftUI (primary UI framework with UIKit interop for drawing canvas)
- PencilKit (Apple Pencil drawing framework, optimized for low latency)
- File-based storage (FileManager + Codable for artwork persistence)
- PHPickerViewController (iOS 14+ photo picker for importing custom coloring pages)
- XCTest (native iOS testing framework)
- Target: iOS 15.0+ on iPad

## Project Structure

```text
ColoringApp/
├── ColoringApp.xcodeproj
├── ColoringApp/
│   ├── App/
│   ├── Features/
│   │   ├── ColoringPageGallery/
│   │   ├── DrawingCanvas/
│   │   ├── BrushTools/
│   │   └── ArtworkGallery/
│   ├── Core/
│   │   ├── Storage/
│   │   └── Utilities/
│   └── Resources/
├── ColoringAppTests/
└── ColoringAppUITests/
```

## Commands

### Build & Run
- Build and run in simulator: `Cmd+R` in Xcode
- Build only: `Cmd+B` in Xcode
- Clean build: `Cmd+Shift+K` in Xcode
- Run tests: `Cmd+U` in Xcode
- Profile with Instruments: `Cmd+I` in Xcode

### Deployment
- Deploy to physical iPad: Connect via USB, select device, `Cmd+R`
- Archive for TestFlight: Product → Archive in Xcode

## Code Style

### Swift Conventions
- Follow [Swift API Design Guidelines](https://swift.org/documentation/api-design-guidelines/)
- Use SwiftUI naming conventions: `View` suffix for views, `ViewModel` suffix for view models
- Protocols: `Protocol` suffix (e.g., `ColoringPageRepositoryProtocol`)
- Use `MARK:` comments to organize code sections
- Prefer `let` over `var` for immutability
- Use explicit types for clarity in complex scenarios
- Async/await for asynchronous operations (Swift 5.5+)

### Architecture Patterns
- MVVM for UI layers (Model-View-ViewModel)
- Protocol-oriented design for repositories and services
- Dependency injection via protocol conformance
- Feature-based modular organization

## Recent Changes

- 001-coloring-pages: Added Swift 5.9+ with SwiftUI + PencilKit architecture for iPad drawing app with Apple Pencil support

<!-- MANUAL ADDITIONS START -->
<!-- MANUAL ADDITIONS END -->
