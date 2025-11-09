# Hybrid Workflow Guide: Windows PC + MacBook Pro

**Setup**: Draft code on Windows PC with GitHub Copilot, build/test on Mac with Xcode  
**Purpose**: Maximize coding time on your primary Windows setup, use Mac efficiently for builds/testing

---

## Quick Reference

### Daily Workflow

```
Monday-Friday (Windows PC)
├── Edit Swift files in VS Code
├── Use Copilot to draft new features
├── Commit to Git when ready
└── Push to GitHub

Weekend (MacBook Pro)
├── Pull latest code
├── Build in Xcode (Cmd+B)
├── Fix compilation errors
├── Test in Simulator (Cmd+R)
├── Deploy to iPad (USB cable)
└── Push fixes to GitHub
```

---

## One-Time Setup

### Windows PC Setup

**1. Install VS Code Extensions**:

```powershell
# Open VS Code, then install extensions (Ctrl+Shift+X):
# - GitHub Copilot
# - GitHub Copilot Chat
# - Swift (sswg.swift-lang)
```

**2. Configure Git** (if not already):

```powershell
git config --global user.name "Your Name"
git config --global user.email "your.email@example.com"
```

**3. Clone Repository**:

```powershell
cd C:\Workspace
git clone <your-repo-url> coloring-mobile-app
cd coloring-mobile-app
code .  # Opens in VS Code
```

**4. Verify Extensions Loaded**:

- Open any `.swift` file (e.g., `specs/001-coloring-pages/research.md`)
- GitHub Copilot icon should appear in status bar
- Try typing `// Function to` and see if Copilot suggests code

---

### MacBook Pro Setup

**1. Clone Same Repository**:

```bash
cd ~/Documents
git clone <your-repo-url> coloring-mobile-app
cd coloring-mobile-app
```

**2. Install Xcode** (if not already):

- Open Mac App Store
- Search "Xcode"
- Install (takes ~30 minutes, ~15GB download)
- Launch Xcode → Agree to license

**3. Sign in to Apple ID**:

- Xcode → Settings → Accounts
- Click "+" → Add Apple ID
- Sign in with your Apple ID

**4. Install Copilot for Xcode** (optional but recommended):

```bash
# Install via Homebrew
brew install --cask copilot-for-xcode

# Or download from: https://github.com/github/CopilotForXcode
```

**5. Grant Copilot Permissions**:

- System Settings → Privacy & Security → Extensions
- Scroll to "Xcode Source Editor"
- Enable "Copilot for Xcode"

**6. Create Xcode Project** (first time only):

- Follow steps in `quickstart.md`
- Create ColoringApp project in `ColoringApp/` folder
- Commit and push to GitHub

---

## Phase 1: Drafting Code on Windows PC

### Starting a New Feature

**1. Create Feature Branch**:

```powershell
# On Windows PC
git checkout -b feature/color-palette-view
```

**2. Create New Swift File**:

```powershell
# In VS Code, create file:
mkdir -p ColoringApp/ColoringApp/Features/BrushTools/Views
code ColoringApp/ColoringApp/Features/BrushTools/Views/ColorPaletteView.swift
```

**3. Use Copilot to Draft Code**:

```swift
// In ColorPaletteView.swift, type a comment:
// SwiftUI view that displays a grid of 12 color buttons
// When a color is tapped, it becomes the selected color

// Copilot will suggest:
import SwiftUI

struct ColorPaletteView: View {
    let colors: [Color]
    @Binding var selectedColor: Color

    var body: some View {
        LazyVGrid(columns: [GridItem(.adaptive(minimum: 60))], spacing: 12) {
            ForEach(colors, id: \.self) { color in
                // ... Copilot continues
            }
        }
    }
}
```

**4. Use Copilot Chat for Learning**:

- Select code → Right-click → "Copilot Chat"
- Ask: "Explain how @Binding works in SwiftUI"
- Ask: "How do I add accessibility labels to these buttons?"
- Ask: "What's the best way to handle color selection state?"

**5. Iterate with Copilot**:

- Add comments describing what you want
- Let Copilot suggest implementations
- Refine until code looks right
- Remember: **It won't compile on Windows**, that's OK!

**6. Commit Draft**:

```powershell
git add ColoringApp/ColoringApp/Features/BrushTools/Views/ColorPaletteView.swift
git commit -m "Draft ColorPaletteView with Copilot"
git push origin feature/color-palette-view
```

### Working on Existing Files

**1. Pull Latest Code**:

```powershell
git checkout main
git pull origin main
```

**2. Open File in VS Code**:

```powershell
code ColoringApp/ColoringApp/Features/ColoringPageGallery/ViewModels/ColoringPageGalleryViewModel.swift
```

**3. Add New Function**:

```swift
// Type comment above existing code:
// Function to load all coloring pages asynchronously

// Copilot suggests:
func loadPages() async {
    do {
        coloringPages = try await repository.loadAll()
    } catch {
        // Handle error
        errorMessage = error.localizedDescription
    }
}
```

**4. Use Copilot for Refactoring**:

- Select existing code
- Copilot Chat: "Refactor this to use async/await"
- Copilot Chat: "Add error handling to this function"

**5. Commit Changes**:

```powershell
git add .
git commit -m "Add async loadPages function"
git push
```

---

## Phase 2: Building & Testing on Mac

### Pull and Build

**1. Switch to MacBook Pro**

**2. Pull Latest Code**:

```bash
cd ~/Documents/coloring-mobile-app
git pull origin feature/color-palette-view
```

**3. Open in Xcode**:

```bash
open ColoringApp/ColoringApp.xcodeproj
```

**4. Build Project** (Cmd+B):

- Xcode compiles your Windows-drafted code
- **Compilation errors are expected!**
- Read error messages carefully

### Fix Compilation Errors

**Common Errors from Windows Drafts**:

**Error: "Cannot find 'ColorPaletteView' in scope"**

```swift
// Fix: Add file to Xcode project
// Right-click folder → Add Files to "ColoringApp"
// Select ColorPaletteView.swift
```

**Error: "Missing import statement"**

```swift
// Fix: Add import at top of file
import SwiftUI  // or UIKit, PencilKit, etc.
```

**Error: "Type 'Color' has no member 'systemRed'"**

```swift
// Fix: Use correct UIKit/SwiftUI color
.foregroundColor(.red)  // SwiftUI
// or
UIColor.systemRed  // UIKit
```

**Error: "@Binding used in wrong context"**

```swift
// Fix: Check if it should be @State or @Binding
// Copilot Chat in Xcode: "When should I use @Binding vs @State?"
```

### Test in Simulator

**1. Select Simulator**:

- Device dropdown → iPad Pro (12.9-inch) (6th generation)

**2. Run** (Cmd+R):

- App builds and launches in Simulator
- Test your new feature visually

**3. Test Interactions**:

- Click/tap to simulate finger touches
- Verify UI looks correct
- Check console for errors (Cmd+Shift+Y shows debug area)

### Test on Physical iPad

**1. Connect iPad**:

- USB cable → MacBook Pro
- iPad: "Trust This Computer?" → Trust

**2. Select iPad**:

- Device dropdown → Your iPad's name

**3. Run on Device** (Cmd+R):

- First time: May need to trust developer certificate (Settings → General → VPN & Device Management)
- App installs and launches

**4. Test Apple Pencil**:

- Draw with Apple Pencil on canvas
- Verify latency is acceptable (<20ms feel)
- Test pressure sensitivity (if supported)
- Test eraser end (if applicable)

### Commit Fixes

**After successful build/test**:

```bash
git add .
git commit -m "Fix compilation errors from ColorPaletteView draft"
git push origin feature/color-palette-view
```

**If feature complete**:

```bash
git checkout main
git merge feature/color-palette-view
git push origin main
```

---

## Phase 3: Iterate (Back to Windows)

**On Windows PC**:

```powershell
# Pull the fixes you made on Mac
git pull origin main

# Continue drafting next feature
git checkout -b feature/eraser-tool
# ... draft with Copilot ...
```

**Cycle repeats**: Draft on Windows → Build/test on Mac → Fix on Mac → Pull on Windows

---

## Tips for Efficient Workflow

### Maximize Windows Time

**Draft complete features**:

- ✅ Write entire Swift files with Copilot
- ✅ Add comments explaining intent
- ✅ Structure code logically (Copilot helps with this)
- ❌ Don't worry about compilation errors yet

**Use Copilot Chat heavily**:

- Learn Swift syntax: "How do I create an optional in Swift?"
- Learn SwiftUI: "Show me a SwiftUI button with custom styling"
- Learn patterns: "What's the MVVM pattern in SwiftUI?"

**Read documentation offline**:

- Apple's Swift book (download PDF)
- SwiftUI tutorials (read without Mac)
- Prepare questions for when you're on Mac

### Maximize Mac Time

**Batch your Mac sessions**:

- Pull all changes from Windows drafts
- Build entire project
- Fix all compilation errors at once
- Test all new features together
- Commit all fixes

**Profile performance** (Mac-only):

- Cmd+I → Instruments
- Run Time Profiler on drawing canvas
- Check for memory leaks
- Verify 60fps in animations

**Use Xcode-specific tools**:

- View Hierarchy Debugger (3D UI view)
- Memory Graph (find leaks)
- Accessibility Inspector (VoiceOver testing)

### Git Best Practices

**Small, frequent commits on Windows**:

```powershell
git commit -m "Draft ColorPaletteView structure"
git commit -m "Add color selection logic to ColorPaletteView"
git commit -m "Add accessibility labels to color buttons"
```

**Fix commits on Mac**:

```bash
git commit -m "Fix: Add missing imports to ColorPaletteView"
git commit -m "Fix: Correct color binding syntax"
```

**Descriptive branch names**:

```
feature/color-palette-view
feature/drawing-canvas
fix/apple-pencil-latency
refactor/repository-layer
```

---

## Troubleshooting

### "Code works on Windows but won't compile on Mac"

**Expected!** Copilot drafts may have:

- Missing imports
- Incorrect API usage
- Typos in type names
- Wrong SwiftUI/UIKit APIs

**Solution**: This is part of learning! Read Xcode's error messages—they're usually clear about what's wrong.

### "Copilot suggests code that looks wrong"

**Copilot isn't perfect**:

- May suggest outdated Swift syntax
- May confuse SwiftUI with UIKit
- May hallucinate APIs that don't exist

**Solution**: Use Copilot as a **starting point**, not gospel. Validate suggestions against Apple's documentation.

### "I can't test code without Mac"

**Accept the limitation**:

- Use Windows for **drafting** and **planning**
- Use Mac for **validation** and **testing**
- Schedule Mac sessions (e.g., weekends)
- This is a feature, not a bug: you'll write more thoughtful code!

### "Git conflicts between Windows and Mac"

**Cause**: Editing same file on both machines without pulling.

**Prevention**:

```powershell
# Always pull before starting work
git pull origin main

# Push when done
git push origin feature/my-feature
```

**Resolution**:

```bash
# If conflict occurs
git pull origin main
# Edit files to resolve conflicts
git add .
git commit -m "Resolve merge conflict"
git push
```

---

## Example Full Cycle

### Monday (Windows PC)

**Morning**:

```powershell
git checkout -b feature/save-artwork
code ColoringApp/ColoringApp/Core/Storage/ArtworkRepository.swift
```

Type in VS Code:

```swift
// Repository protocol for saving and loading artwork
// Uses file-based storage with Codable

// Copilot suggests full protocol definition
```

**Afternoon**:

```swift
// Implement concrete repository class
// Save artwork to Documents directory using FileManager

// Copilot suggests implementation
```

**Evening**:

```powershell
git add .
git commit -m "Draft ArtworkRepository with file-based storage"
git push origin feature/save-artwork
```

### Saturday (MacBook Pro)

**Morning**:

```bash
git pull origin feature/save-artwork
open ColoringApp/ColoringApp.xcodeproj
```

Cmd+B → Fix errors:

- Add `import Foundation`
- Fix `FileManager` API usage
- Correct `Codable` syntax

**Afternoon**:
Cmd+R → Test in Simulator:

- Save an artwork
- Quit app
- Relaunch app
- Verify artwork persists

**Test on iPad**:

- Draw with Apple Pencil
- Save artwork
- Verify file created

**Evening**:

```bash
git add .
git commit -m "Fix ArtworkRepository implementation, tested on device"
git push origin feature/save-artwork
git checkout main
git merge feature/save-artwork
git push origin main
```

### Monday (Windows PC)

**Morning**:

```powershell
git pull origin main  # Gets your Mac fixes
git checkout -b feature/artwork-gallery
# Start next feature...
```

---

## Advanced: VS Code Remote-SSH (Optional)

If you want to edit code **on the Mac** from your Windows PC:

**On MacBook Pro**:

```bash
# Enable Remote Login
# System Settings → General → Sharing → Remote Login: ON
```

**On Windows PC**:

1. Install "Remote - SSH" extension in VS Code
2. Cmd+Shift+P → "Remote-SSH: Connect to Host"
3. Enter: `your-username@macbook-ip-address`
4. Edit Swift files directly on Mac via SSH
5. Open terminal, run `xcodebuild` commands

**Pros**:

- Edit Mac files from Windows
- Run builds via terminal
- Don't need to push/pull Git

**Cons**:

- Can't see Simulator (terminal only)
- More complex setup
- Need Mac to be on and awake

---

## Summary

✅ **Windows PC**: Draft Swift code with Copilot → Git commit → Git push  
✅ **MacBook Pro**: Git pull → Build in Xcode → Fix errors → Test → Git push  
✅ **Repeat**: This workflow lets you code daily without blocking on Mac access

**Key mindset**: Windows drafting is **thinking time**. Mac building is **validation time**. Both are essential!

Happy coding! 🎨💻
