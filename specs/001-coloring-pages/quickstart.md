# Quickstart Guide: Coloring & Drawing Studio

**Feature**: 001-coloring-pages  
**Audience**: First-time iOS developers  
**Purpose**: Step-by-step guide to set up development environment, build, and deploy the app

---

## Prerequisites

### System Requirements

- **Mac Computer**: macOS Ventura (13.0) or later recommended
- **Disk Space**: ~50GB free (Xcode + iOS Simulators)
- **Apple ID**: Free account sufficient to start (can upgrade to paid later)
- **iPad** (optional for physical testing): iPad with iOS 15.0+ and Apple Pencil support

### What You'll Install

1. **Xcode 15+**: Apple's IDE for iOS development (free from Mac App Store)
2. **Command Line Tools**: Automatically installed with Xcode
3. **iOS Simulators**: Virtual iPad for testing (bundled with Xcode)

---

## Step 1: Install Xcode

### Installation

1. **Open Mac App Store**
   - Click Apple menu () → App Store
   - Search for "Xcode"
   - Click "Get" then "Install" (requires Apple ID sign-in)
   - Wait ~30 minutes for download/installation (large file)

2. **First Launch**
   - Open Xcode from Applications folder or Launchpad
   - Click "Agree" to license agreement
   - Xcode will install additional components (Command Line Tools, iOS Simulator)
   - Wait for installation to complete

3. **Verify Installation**
   - Open Terminal (Applications → Utilities → Terminal)
   - Run: `xcode-select --version`
   - Should output something like: `xcode-select version 2396.`
   - Run: `xcrun --version`
   - Should output: `xcrun version 65.`

### Troubleshooting

**Issue**: "Command Line Tools not found"  
**Solution**: Run `xcode-select --install` in Terminal

**Issue**: Xcode won't open or crashes  
**Solution**: Check macOS version (needs 13.0+), ensure 50GB+ free space, restart Mac

---

## Step 2: Set Up Apple Developer Account

### Free Account (Sufficient for Learning)

1. **Sign In to Xcode**
   - Open Xcode
   - Xcode menu → Settings (or Preferences on older Xcode)
   - Click "Accounts" tab
   - Click "+" button → "Apple ID"
   - Sign in with your Apple ID (same one used for App Store, iCloud, etc.)
   - Xcode creates a "Personal Team" tied to your Apple ID

2. **Understand Limitations**
   - ✅ Can install apps on your own devices (up to 3 devices)
   - ✅ Full access to all APIs and frameworks (PencilKit, etc.)
   - ❌ Apps expire after 7 days (must reinstall)
   - ❌ No TestFlight distribution
   - ❌ Cannot publish to App Store

### Paid Account (Optional - $99/year)

**When to upgrade**:
- You want apps to last 90 days per build (TestFlight)
- You want to test on more than 3 devices
- You want to share with family/friends via TestFlight
- You eventually want to publish to App Store

**How to enroll**:
1. Visit https://developer.apple.com/programs/
2. Click "Enroll"
3. Complete enrollment form
4. Pay $99 via credit card
5. Approval usually within 24-48 hours
6. In Xcode → Accounts, your Personal Team becomes "Your Name (Individual)"

---

## Step 3: Create the Xcode Project

### Create New Project

1. **Launch Xcode**
2. **Start New Project**
   - Welcome screen → "Create a new Xcode project"
   - Or: File menu → New → Project (Cmd+Shift+N)

3. **Choose Template**
   - iOS tab → "App"
   - Click "Next"

4. **Configure Project**
   - **Product Name**: `ColoringApp` (no spaces)
   - **Team**: Select your Personal Team from dropdown
   - **Organization Identifier**: `com.yourname` (e.g., `com.johnsmith`)
     - This + Product Name = Bundle ID: `com.yourname.ColoringApp`
   - **Interface**: **SwiftUI** (not Storyboard!)
   - **Language**: **Swift**
   - **Storage**: Leave unchecked (we're using files, not CoreData)
   - **Include Tests**: Check both boxes (Unit Tests, UI Tests)
   - Click "Next"

5. **Choose Location**
   - Navigate to your workspace: `c:\Workspace\coloring-mobile-app` (if on Windows VM) or similar
   - ❌ DO NOT check "Create Git repository" (already have one)
   - Click "Create"

### Project Structure Created

```text
ColoringApp/
├── ColoringApp.xcodeproj       # Xcode project file (double-click to open)
├── ColoringApp/                # Main app code
│   ├── ColoringAppApp.swift   # App entry point
│   ├── ContentView.swift      # Initial SwiftUI view
│   ├── Assets.xcassets/       # Images, colors, icons
│   └── Preview Content/       # Preview assets for Xcode Canvas
├── ColoringAppTests/           # Unit tests
└── ColoringAppUITests/         # UI tests
```

---

## Step 4: Configure Code Signing

### Automatic Signing (Recommended)

1. **Open Project Settings**
   - In Xcode, click the blue "ColoringApp" project icon in the left sidebar (Project Navigator)
   - Select "ColoringApp" target (under TARGETS, not PROJECT)

2. **Signing & Capabilities Tab**
   - Click "Signing & Capabilities" at the top
   - Ensure "Automatically manage signing" is **checked** ✅
   - **Team**: Should auto-select your Personal Team
   - **Bundle Identifier**: Should show `com.yourname.ColoringApp`

3. **Verify No Errors**
   - Should see green checkmark ✅ "Signing for 'ColoringApp' requires a development team. Select a development team in the Signing & Capabilities editor."
   - If you see red ❌ error, click "Try Again" or select your team manually

### What This Does

- Xcode generates a **signing certificate** (proves you're the developer)
- Xcode creates a **provisioning profile** (links your app to your devices)
- All managed automatically—you don't need to understand the details

---

## Step 5: Run in Simulator

### Choose a Simulator

1. **Device Dropdown** (top toolbar, next to scheme dropdown)
   - Click the device dropdown (defaults to "iPhone 15 Pro" or similar)
   - Scroll to "iPad" section
   - Select **"iPad Pro (12.9-inch) (6th generation)"**
     - This simulates the 12.9" iPad Pro your kids will use
     - Has ProMotion display (120Hz) for testing smoothness

2. **Download Simulator Runtime** (if needed)
   - If you see a cloud icon ☁️ next to the simulator name, you need to download it
   - Xcode → Settings → Platforms → iOS → "+" → Download desired iOS version
   - Wait for download (~5GB)

### Build and Run

1. **Press Play Button** (top-left toolbar) or **Cmd+R**
   - Xcode will:
     - Compile Swift code (first build takes ~1-2 minutes)
     - Install app on simulator
     - Launch simulator
     - Open your app

2. **Simulator Launches**
   - A window opens showing an iPad screen
   - Your app launches automatically
   - You'll see "Hello, world!" text (default ContentView)

3. **Interact with Simulator**
   - **Click** = tap with finger
   - **Scroll**: Click and drag
   - **Home button**: Device → Home (Cmd+Shift+H)
   - **Rotate**: Device → Rotate Left/Right (Cmd+←/→)

### Simulator Limitations

- ❌ **No Apple Pencil support** (can't test pressure, tilt, or latency)
- ❌ **Different performance** (faster CPU, no GPU like real iPad)
- ✅ **Good for**: UI layout, navigation, logic testing
- ✅ **Fast iteration**: No device connection, instant deploy

**Recommendation**: Use simulator for rapid development, test on physical iPad regularly.

---

## Step 6: Deploy to Physical iPad

### Prerequisites

- iPad with iOS 15.0+ (check Settings → General → About → Software Version)
- Lightning or USB-C cable to connect iPad to Mac
- iPad unlocked and awake

### First-Time Setup

1. **Connect iPad to Mac**
   - Use cable to connect iPad to Mac
   - iPad shows popup: "Trust This Computer?"
   - Tap "Trust"
   - Enter iPad passcode if prompted

2. **iPad Appears in Xcode**
   - In Xcode, the device dropdown now shows your iPad's name
   - Example: "Nick's iPad (15.6)"
   - Select your iPad from the dropdown

3. **Build to iPad**
   - Press Play button (Cmd+R)
   - Xcode builds and installs on iPad
   - **First time only**: iPad shows error "Untrusted Developer"

4. **Trust Developer Certificate**
   - On iPad: Settings → General → VPN & Device Management
   - Under "Developer App", tap your Apple ID
   - Tap "Trust [Your Apple ID]"
   - Confirm "Trust"

5. **Run Again**
   - In Xcode, press Play (Cmd+R) again
   - App installs and launches on iPad
   - 🎉 Your app is now running on physical hardware!

### Testing with Apple Pencil

1. **Pair Apple Pencil** (if not already paired)
   - Plug Apple Pencil into iPad (Lightning) or attach magnetically (2nd gen)
   - Follow on-screen pairing prompts

2. **Test Drawing**
   - Currently, the default app won't respond to Apple Pencil (no drawing canvas yet)
   - After implementing PencilKit canvas (later phases), test:
     - ✅ Stroke appears immediately as you draw
     - ✅ Pressure affects stroke thickness
     - ✅ Tilt affects stroke shape (if using pencil ink type)
     - ✅ No visible lag (<20ms latency target)

### 7-Day App Expiration

**Free Account Limitation**: Apps expire 7 days after installation.

**What Happens**:
- Day 8: App won't launch, shows "Unable to Verify App" error
- Must reconnect iPad to Mac and rebuild (Cmd+R) to refresh signature

**Workaround**:
- Rebuild weekly while actively developing
- Consider upgrading to paid account ($99/year) if this becomes annoying
- Paid account: Apps last 90 days via TestFlight, no expiration for local builds

---

## Step 7: Understand Xcode Interface

### Key Areas

```text
┌────────────────────────────────────────────────────────┐
│  Toolbar: Build, Run, Stop, Device Dropdown, Scheme   │
├──────────┬─────────────────────────────────┬───────────┤
│          │                                 │           │
│ Navigator│       Editor Area               │ Inspector │
│  (Files) │     (Code, Canvas, Debugger)    │ (Details) │
│          │                                 │           │
│          │                                 │           │
├──────────┴─────────────────────────────────┴───────────┤
│              Debug Area (Console, Variables)           │
└────────────────────────────────────────────────────────┘
```

**Left Sidebar (Navigator)**:
- **Project Navigator** (folder icon): File tree
- **Search Navigator** (magnifying glass): Find in project
- **Issue Navigator** (warning icon): Errors and warnings
- **Test Navigator** (beaker icon): Unit tests
- **Debug Navigator** (gauge icon): Performance profiling

**Center (Editor)**:
- **Code Editor**: Where you write Swift code
- **Canvas** (SwiftUI): Live preview of UI (appears when viewing SwiftUI file)
- **Interface Builder**: Visual editor (if using Storyboards—we're not)

**Right Sidebar (Inspector)**:
- **File Inspector**: File properties
- **Quick Help**: Documentation for selected symbol
- **Attributes Inspector**: UI element properties

**Bottom (Debug Area)**:
- **Console**: Print statements, logs, errors
- **Variables View**: Inspect values during debugging
- Toggle visibility: Cmd+Shift+Y

### Essential Keyboard Shortcuts

| Action | Shortcut | Use Case |
|--------|----------|----------|
| Build & Run | Cmd+R | Compile and launch app |
| Stop | Cmd+. | Stop running app |
| Build | Cmd+B | Compile without running (check for errors) |
| Clean Build | Cmd+Shift+K | Delete build artifacts (fixes weird issues) |
| Quick Open | Cmd+Shift+O | Jump to file by name |
| Find in Project | Cmd+Shift+F | Search all files |
| Show/Hide Debug Area | Cmd+Shift+Y | Toggle console |
| Show/Hide Navigators | Cmd+0 | Toggle left sidebar |
| Show/Hide Inspectors | Cmd+Option+0 | Toggle right sidebar |
| Comment/Uncomment | Cmd+/ | Toggle code comments |

---

## Step 8: Project Organization

### Recommended Folder Structure

As you build the app, organize files into groups (folders in Xcode Navigator):

1. **Create Groups**:
   - Right-click "ColoringApp" folder in Project Navigator
   - New Group → Name it (e.g., "Features")
   - Repeat for each group below

2. **Structure**:

```text
ColoringApp/
├── App/
│   └── ColoringAppApp.swift        # App entry point (already exists)
├── Features/
│   ├── ColoringPageGallery/
│   │   ├── Views/
│   │   │   └── ColoringPageGalleryView.swift
│   │   ├── ViewModels/
│   │   │   └── ColoringPageGalleryViewModel.swift
│   │   └── Models/
│   │       └── ColoringPage.swift
│   ├── DrawingCanvas/
│   │   ├── Views/
│   │   ├── ViewModels/
│   │   └── DrawingEngine/
│   ├── BrushTools/
│   └── ArtworkGallery/
├── Core/
│   ├── Storage/
│   │   ├── ColoringPageRepository.swift
│   │   └── ArtworkRepository.swift
│   ├── Extensions/
│   └── Utilities/
├── Resources/
│   ├── Assets.xcassets/            # Already exists
│   └── ColoringPages/              # Create folder, add built-in images
└── Preview Content/                # Already exists
```

3. **Add Files to Groups**:
   - Right-click group → New File → Swift File
   - Or drag existing files into groups

### Built-in Coloring Pages

1. **Add Images to Project**:
   - Drag PNG files into "Resources/ColoringPages" group
   - Dialog appears: Check "Copy items if needed" ✅
   - "Add to targets": Check "ColoringApp" ✅
   - Click "Finish"

2. **Verify Bundle Resources**:
   - Project Settings → ColoringApp target → Build Phases
   - Expand "Copy Bundle Resources"
   - Should see your coloring page images listed
   - These files will be included in the app bundle

---

## Step 9: Version Control Integration

### Git Setup

**If using GitHub/Git**:

1. **Initialize Git** (if not already done):
   ```bash
   cd /path/to/ColoringApp
   git init
   git add .
   git commit -m "Initial Xcode project"
   ```

2. **Xcode Git Integration**:
   - Xcode menu → Integrate → Show Source Control Navigator (Cmd+2)
   - See commits, branches, changes
   - Right-click files → Source Control → Commit/Discard changes

3. **What to Commit**:
   - ✅ `.xcodeproj/project.pbxproj` (project settings)
   - ✅ All `.swift` files
   - ✅ `Assets.xcassets/`
   - ❌ `*.xcuserstate` (user-specific settings)
   - ❌ `xcuserdata/` (user-specific data)
   - ❌ `DerivedData/` (build artifacts)
   - ❌ `.DS_Store` (macOS metadata)

4. **Recommended .gitignore**:
   ```gitignore
   # Xcode
   xcuserdata/
   *.xcuserstate
   DerivedData/
   *.hmap
   *.ipa
   *.dSYM.zip
   *.dSYM
   
   # macOS
   .DS_Store
   ```

---

## Step 10: Debugging Basics

### Print Debugging

**Quick way to see values**:

```swift
let coloringPages = try await repository.loadAll()
print("Loaded \(coloringPages.count) coloring pages")  // Output to console
```

**Better**: Use Swift's `dump()` for detailed output:

```swift
dump(coloringPage)  // Shows all properties
```

### Breakpoints

**Pause code execution to inspect state**:

1. Click line number gutter in code editor → blue arrow appears (breakpoint set)
2. Run app (Cmd+R)
3. When code reaches breakpoint, app pauses
4. **Debug Area** shows:
   - Variables and their current values
   - Call stack (how you got here)
5. **Controls** (bottom toolbar):
   - Continue (Cmd+Ctrl+Y): Resume execution
   - Step Over (F6): Execute current line, move to next
   - Step Into (F7): Dive into function call
   - Step Out (F8): Finish current function

### View Hierarchy Debugger

**Visual inspection of UI**:

1. Run app on simulator or device
2. Pause execution (Cmd+Ctrl+Y or click pause button)
3. Click "Debug View Hierarchy" button (3D stack icon in debug toolbar)
4. 3D representation of all UI elements
5. Click layers to see properties in Inspector

### Common Errors

**"No such module 'PencilKit'"**:
- **Cause**: Missing import statement
- **Fix**: Add `import PencilKit` at top of file

**"Failed to register bundle identifier"**:
- **Cause**: Bundle ID conflict (another app with same ID)
- **Fix**: Change Bundle Identifier in project settings (add unique suffix)

**"Unable to install [app]"**:
- **Cause**: Device storage full, or app already installed with different signature
- **Fix**: Delete app from iPad, clean build folder (Cmd+Shift+K), rebuild

**"Command PhaseScriptExecution failed"**:
- **Cause**: Build script error (usually code signing)
- **Fix**: Clean build folder, check code signing settings, restart Xcode

---

## Step 11: Performance Profiling

### Instruments (Performance Analysis Tool)

**When to use**: After implementing drawing canvas, verify performance targets (<20ms latency, 60fps).

1. **Launch Instruments**:
   - Xcode → Product → Profile (Cmd+I)
   - Choose template:
     - **Time Profiler**: Find slow code (CPU usage)
     - **Allocations**: Track memory usage
     - **Leaks**: Find memory leaks

2. **Run Profiling Session**:
   - Instruments launches with your app
   - Use app normally (draw many strokes)
   - Click record button to start collecting data
   - Click stop when done

3. **Analyze Results**:
   - **Time Profiler**: Shows which functions consume most CPU time
     - Expand call stacks to find bottlenecks
     - Goal: No single function >50ms on main thread
   - **Allocations**: Shows memory growth over time
     - Goal: Stable memory (no leaks), <150MB usage

### Built-in Debug Gauges

**Quick performance check without Instruments**:

1. **Show Debug Navigator** (Cmd+7)
2. Run app (Cmd+R)
3. Debug Navigator shows live graphs:
   - **CPU**: Should be low when idle, spikes during drawing OK
   - **Memory**: Should be stable, <150MB for this app
   - **Disk**: Should be near zero (only spikes during save)
   - **Network**: Should be zero (offline app per constitution)

---

## Step 12: TestFlight Distribution (Optional)

**Requires**: Paid Apple Developer Program ($99/year)

### When to Use TestFlight

- Share with family members who don't have physical access to your Mac
- Test on multiple iPads without 7-day expiration hassle
- Get feedback from non-developers

### TestFlight Setup

1. **Enroll in Apple Developer Program** (see Step 2)

2. **Create App in App Store Connect**:
   - Visit https://appstoreconnect.apple.com/
   - Click "+" → New App
   - Fill in details:
     - Platform: iOS
     - Name: Coloring & Drawing Studio
     - Primary Language: English
     - Bundle ID: Select your com.yourname.ColoringApp
     - SKU: ColoringApp (unique identifier, not shown to users)
   - Click "Create"

3. **Archive App in Xcode**:
   - Select "Any iOS Device (arm64)" from device dropdown (not simulator!)
   - Product → Archive
   - Wait for build to complete (~2-5 minutes)
   - Xcode Organizer opens showing archives

4. **Distribute to TestFlight**:
   - Select archive → "Distribute App"
   - Choose "App Store Connect" → Next
   - Choose "Upload" → Next
   - Sign with your certificate (automatic) → Upload
   - Wait for upload (~5-10 minutes depending on app size)

5. **Add Testers in App Store Connect**:
   - App Store Connect → TestFlight tab
   - Internal Testing → "+" → Add Internal Testers (up to 100)
   - Or External Testing → Create group → Add testers by email (up to 10,000)
   - Testers receive email with TestFlight invite

6. **Testers Install App**:
   - Install TestFlight app from App Store on iPad
   - Open invite link from email
   - Tap "Install" in TestFlight
   - App installs and lasts 90 days per build

### TestFlight vs Sideloading Comparison

| Feature | Free (Sideload) | Paid (TestFlight) |
|---------|-----------------|-------------------|
| **Cost** | Free | $99/year |
| **App Duration** | 7 days | 90 days per build |
| **Device Limit** | 3 devices | 100 testers (internal) / 10,000 (external) |
| **Distribution** | USB cable only | Over-the-air (email invite) |
| **Setup Complexity** | Low | Medium |
| **Best For** | Solo development, learning | Family sharing, beta testing |

---

## Step 13: Common Development Workflows

### Daily Development Loop

```text
1. Open Xcode → Open project (ColoringApp.xcodeproj)
2. Select iPad simulator from device dropdown
3. Make code changes
4. Press Cmd+R to build & run
5. Test in simulator
6. If issue found → fix code → repeat step 4
7. Once feature works in simulator → test on physical iPad
8. Commit to Git when feature is complete
```

### Adding a New Feature

```text
1. Create feature branch: `git checkout -b feature/new-feature`
2. Create folder group in Xcode (Features/NewFeature/)
3. Add View, ViewModel, Model files
4. Write unit tests for ViewModel logic
5. Test feature in simulator
6. Test feature on iPad with Apple Pencil
7. Commit changes: `git commit -m "Add new feature"`
8. Merge to main: `git checkout main && git merge feature/new-feature`
```

### Fixing a Bug

```text
1. Reproduce bug (simulator or device)
2. Add print statements or breakpoints to investigate
3. Identify root cause
4. Write a unit test that fails (demonstrates bug)
5. Fix code
6. Verify test now passes
7. Test full app to ensure no regressions
8. Commit fix: `git commit -m "Fix: description of bug"`
```

---

## Troubleshooting Guide

### Build Failures

**"Swift Compiler Error"**:
- **Cause**: Syntax error or type mismatch in Swift code
- **Fix**: Read error message, click error to jump to line, fix syntax

**"Linker Error"**:
- **Cause**: Missing framework or file
- **Fix**: Check that all files are added to target (Project Settings → Build Phases → Compile Sources)

### Simulator Issues

**"Simulator not responding"**:
- **Fix**: Simulator → Device → Erase All Content and Settings, restart Xcode

**"App not installing"**:
- **Fix**: Clean build folder (Cmd+Shift+K), quit simulator, rebuild

### Device Issues

**"Device not appearing in Xcode"**:
- **Fix**: Unplug/replug cable, unlock iPad, trust computer again, restart Xcode

**"App installed but won't launch"**:
- **Fix**: Check device logs: Window → Devices and Simulators → Select device → Open Console

### Xcode Crashing

**"Xcode unexpectedly quits"**:
- **Fix**: Delete DerivedData folder: `~/Library/Developer/Xcode/DerivedData/`, restart Mac

### Git Conflicts

**"Merge conflict in project.pbxproj"**:
- **Cause**: Two people (or branches) modified project settings simultaneously
- **Fix**: Complex—safer to resolve by accepting one version, then re-apply changes in Xcode

---

## Next Steps

### Phase 1: Implement P1 Features

1. **Set up data models** (ColoringPage, Artwork structs from data-model.md)
2. **Implement ColoringPageRepository** (file-based storage)
3. **Build ColoringPageGallery view** (SwiftUI grid showing thumbnails)
4. **Implement DrawingCanvas** (PencilKit integration)
5. **Add BrushTools** (color palette, eraser button)
6. **Test P1 acceptance criteria** (User Stories 1-3 from spec.md)

### Phase 2: Implement P2 Features

7. **Add ArtworkRepository** (save/load drawings)
8. **Build ArtworkGallery view** (saved artwork browser)
9. **Implement custom coloring page import** (PHPickerViewController)
10. **Test P2 acceptance criteria** (User Stories 4-5 from spec.md)

### Phase 3: Implement P3 Features (Optional)

11. **Research special brush effects** (rainbow, sparkle)
12. **Implement custom rendering** (if PencilKit insufficient)
13. **Polish UI/UX** (animations, child-friendly icons)
14. **Test P3 acceptance criteria** (User Stories 6-7 from spec.md)

---

## Additional Resources

### Apple Documentation

- [Xcode Documentation](https://developer.apple.com/documentation/xcode)
- [Swift Language Guide](https://docs.swift.org/swift-book/)
- [SwiftUI Tutorials](https://developer.apple.com/tutorials/swiftui)
- [PencilKit Documentation](https://developer.apple.com/documentation/pencilkit)
- [Human Interface Guidelines](https://developer.apple.com/design/human-interface-guidelines/)

### WWDC Sessions (Video Tutorials)

- WWDC 2023: "SwiftUI Essentials"
- WWDC 2019: "Introducing PencilKit"
- WWDC 2023: "What's new in Xcode 15"
- WWDC 2022: "Meet Swift Async/Await"

### Learning Platforms

- [Hacking with Swift](https://www.hackingwithswift.com/) - Free Swift tutorials
- [100 Days of SwiftUI](https://www.hackingwithswift.com/100/swiftui) - Structured course
- [Apple Developer Forums](https://developer.apple.com/forums/) - Ask questions
- [Stack Overflow](https://stackoverflow.com/questions/tagged/swift) - Community Q&A

---

## Getting Help

### When Stuck

1. **Read the error message carefully** (Xcode errors are usually descriptive)
2. **Search the error message** on Google or Stack Overflow
3. **Check Apple documentation** for the API you're using
4. **Ask in Apple Developer Forums** (include code snippet, error message, Xcode version)
5. **Simplify the problem** (create minimal reproducible example)

### Sharing Code for Help

**Good question format**:

```text
I'm trying to [what you want to achieve].

I expect [expected behavior].

Instead, I'm seeing [actual behavior/error message].

Here's my code:
[minimal code snippet]

Xcode version: 15.1
iOS version: 15.6
Device: iPad Pro 12.9" (6th gen)
```

---

## Congratulations! 🎉

You've completed the development environment setup and learned the fundamentals of iOS development workflow. You're ready to start building the Coloring & Drawing Studio app.

**Remember**: Learning iOS development is a journey. Don't get discouraged by errors or challenges—every developer encounters them. The key is systematic problem-solving and patience.

**Happy coding!** 👨‍💻👩‍💻
