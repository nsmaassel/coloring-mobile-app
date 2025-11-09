# Coloring & Drawing Studio

An iPad coloring app for children (ages 3-8) with Apple Pencil support, built as a learning project for iOS development.

## Quick Start

**First-time setup?** → Read [quickstart.md](specs/001-coloring-pages/quickstart.md) for complete step-by-step instructions.

### Prerequisites

**Development Setup** (Hybrid Workflow):

- **Windows PC**: VS Code + GitHub Copilot (for coding/drafting)
- **MacBook Pro**: macOS Ventura 13.0+ with Xcode 15+ (for building/testing)
- **Apple ID**: Free account sufficient to start
- **iPad**: iOS 15.0+ with Apple Pencil (for testing)

**Workflow**: Draft Swift code on Windows PC with Copilot, build/test on Mac with Xcode.

### VS Code Setup

This workspace is configured for VS Code + GitHub Copilot assistance:

1. **Install Recommended Extensions**:

   - Open VS Code
   - View → Extensions (Cmd+Shift+X)
   - Install recommended extensions (notification should appear)
   - Key extensions: GitHub Copilot, Swift Language Support

2. **Open Project in Xcode** (primary development):

   ```bash
   open ColoringApp/ColoringApp.xcodeproj
   ```

3. **Use VS Code for** (secondary):
   - Editing Swift files with Copilot suggestions
   - Reviewing documentation (specs/, research.md, etc.)
   - Git operations and file management
   - Terminal commands

### Why Hybrid Workflow?

**VS Code on Windows** cannot build/run iOS apps, but is **excellent** for:

- 📝 Drafting Swift code with GitHub Copilot's full assistance
- 💬 Copilot Chat for learning and explanations
- 📚 Documentation and planning
- 🔧 Git operations without needing Mac

**Xcode on Mac** is **required** for:

- 🔨 Building iOS apps
- 📱 Running iOS Simulator
- 🎯 Testing on physical iPad
- 🐛 Debugging and profiling

**Result**: You can code anytime on Windows, build/test when Mac is available.

## Project Structure

```
ColoringApp/                     # Xcode project (create after reading quickstart)
├── ColoringApp.xcodeproj
├── ColoringApp/
│   ├── App/
│   ├── Features/
│   ├── Core/
│   └── Resources/
└── ColoringAppTests/

specs/                           # Feature documentation
└── 001-coloring-pages/
    ├── spec.md                 # Feature specification
    ├── plan.md                 # Implementation plan
    ├── research.md             # Technology research
    ├── data-model.md           # Data architecture
    ├── quickstart.md           # Setup guide (START HERE!)
    └── contracts/              # Protocol interfaces
```

## Development Workflow

### Using Xcode (Primary)

```bash
# Open project
open ColoringApp/ColoringApp.xcodeproj

# Build and run: Press Cmd+R in Xcode
# Run tests: Press Cmd+U in Xcode
# Clean build: Press Cmd+Shift+K in Xcode
```

### Using VS Code + Terminal (Secondary)

```bash
# Build (from VS Code terminal)
xcodebuild -project ColoringApp/ColoringApp.xcodeproj \
  -scheme ColoringApp \
  -sdk iphonesimulator \
  -destination 'platform=iOS Simulator,name=iPad Pro (12.9-inch) (6th generation)' \
  build

# Run tests
xcodebuild test -project ColoringApp/ColoringApp.xcodeproj \
  -scheme ColoringApp \
  -destination 'platform=iOS Simulator,name=iPad Pro (12.9-inch) (6th generation)'

# Clean
xcodebuild clean -project ColoringApp/ColoringApp.xcodeproj -scheme ColoringApp
```

Or use VS Code tasks: `Terminal → Run Task → Build iOS App (Simulator)`

## Features

### Phase 1 (P1 - MVP)

- ✅ [Planned] Select and open coloring page
- ✅ [Planned] Color with basic brushes (finger + Apple Pencil)
- ✅ [Planned] Erase and undo mistakes

### Phase 2 (P2)

- ✅ [Planned] Save completed artwork
- ✅ [Planned] Add custom coloring pages

### Phase 3 (P3 - Polish)

- ✅ [Planned] Use special brushes (rainbow, sparkle)
- ✅ [Planned] Start fresh with new page

## Technology Stack

- **Language**: Swift 5.9+
- **UI Framework**: SwiftUI (with UIKit for PencilKit)
- **Drawing**: PencilKit (Apple Pencil optimization)
- **Storage**: File-based (FileManager + Codable)
- **Testing**: XCTest
- **Target**: iOS 15.0+ on iPad

## Architecture

- **Pattern**: MVVM (Model-View-ViewModel)
- **Design**: Protocol-oriented with dependency injection
- **Organization**: Feature-based modules

## Documentation

- **[quickstart.md](specs/001-coloring-pages/quickstart.md)** - Complete setup guide for first-time iOS developers
- **[research.md](specs/001-coloring-pages/research.md)** - Technology decisions and learning resources
- **[data-model.md](specs/001-coloring-pages/data-model.md)** - Data structures and persistence
- **[contracts/](specs/001-coloring-pages/contracts/)** - Protocol interfaces
- **[spec.md](specs/001-coloring-pages/spec.md)** - Feature requirements and acceptance criteria

## Learning Resources

- [Swift Language Guide](https://docs.swift.org/swift-book/)
- [SwiftUI Tutorials](https://developer.apple.com/tutorials/swiftui)
- [PencilKit Documentation](https://developer.apple.com/documentation/pencilkit)
- [Hacking with Swift](https://www.hackingwithswift.com/)
- See [research.md](specs/001-coloring-pages/research.md) for comprehensive list

## Contributing

This is a personal learning project. See [constitution.md](.specify/memory/constitution.md) for project principles.

## License

Personal/educational use only.

---

**Next Steps**: Read [quickstart.md](specs/001-coloring-pages/quickstart.md) to set up your development environment!
