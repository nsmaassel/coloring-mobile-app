# Tasks: Interactive Coloring Pages

**Branch**: `001-coloring-pages` | **Generated**: 2025-11-09  
**Input**: Design documents from `specs/001-coloring-pages/` ([spec.md](./spec.md), [plan.md](./plan.md), [data-model.md](./data-model.md), [contracts/](./contracts/), [research.md](./research.md))

**Tests**: Not explicitly requested in spec - focusing on implementation tasks only.

**Organization**: Tasks grouped by user story to enable independent implementation and testing of each story.

## Format: `[ID] [P?] [Story] Description`

- **[P]**: Can run in parallel (different files, no dependencies)
- **[Story]**: Which user story this task belongs to (e.g., US1, US2, US3)
- Include exact file paths in descriptions

## Path Conventions

iOS Xcode project structure per [plan.md](./plan.md):

- `ColoringApp/ColoringApp/` - Main app target source code
- `ColoringApp/ColoringAppTests/` - Unit tests
- `ColoringApp/ColoringAppUITests/` - UI automation tests

---

## Phase 1: Setup (Shared Infrastructure)

**Purpose**: Xcode project initialization and basic structure on Mac

- [ ] T001 Create Xcode project ColoringApp.xcodeproj following quickstart.md Section 3 (iOS App template, iPad only, iOS 15.0+)
- [ ] T002 [P] Add 3 built-in coloring page PNG images to ColoringApp/Resources/ColoringPages/ (simple line art for ages 3-8)
- [ ] T003 [P] Configure automatic code signing with free Apple ID in Xcode project settings
- [ ] T004 Test build on iPad Simulator to verify project setup

---

## Phase 2: Foundational (Blocking Prerequisites)

**Purpose**: Core protocols and storage infrastructure that ALL user stories depend on

**⚠️ CRITICAL**: No user story work can begin until this phase is complete

- [ ] T005 [P] Create ColoringPage model (Codable struct) in ColoringApp/Features/ColoringPageGallery/Models/ColoringPage.swift per data-model.md Section 1
- [ ] T006 [P] Create Artwork model (Codable struct) in ColoringApp/Core/Storage/Artwork.swift per data-model.md Section 2
- [ ] T007 [P] Create Brush model (struct) in ColoringApp/Features/BrushTools/Models/Brush.swift per data-model.md Section 4
- [ ] T008 [P] Define ColoringPageRepositoryProtocol in ColoringApp/Core/Storage/ColoringPageRepository.swift per contracts/ README.md Section 1
- [ ] T009 [P] Define ArtworkRepositoryProtocol in ColoringApp/Core/Storage/ArtworkRepository.swift per contracts/ README.md Section 2
- [ ] T010 [P] Define DrawingEngineProtocol in ColoringApp/Core/Storage/DrawingEngine.swift per contracts/ README.md Section 3
- [ ] T011 [P] Create StorageError enum in ColoringApp/Core/Storage/StorageError.swift per data-model.md validation section
- [ ] T012 Implement ColoringPageRepository (file-based storage) in ColoringApp/Core/Storage/ColoringPageRepository.swift (depends on T005, T008, T011)
- [ ] T013 Create FileManager extensions for directory management in ColoringApp/Core/Utilities/FileManagerExtensions.swift

**Checkpoint**: Foundation ready - user story implementation can now begin in parallel

---

## Phase 3: User Story 1 - Select and Open Coloring Page (Priority: P1) 🎯 MVP

**Goal**: Child can launch app, see gallery of coloring pages, tap one, and see it load full-screen

**Independent Test**: Launch app → see 3+ thumbnails → tap one → page loads in canvas within 1 second

### Implementation for User Story 1

- [ ] T014 [P] [US1] Create ColoringPageGalleryView (SwiftUI) in ColoringApp/Features/ColoringPageGallery/Views/ColoringPageGalleryView.swift (LazyVGrid with thumbnails)
- [ ] T015 [P] [US1] Create ColoringPageGalleryViewModel (ObservableObject) in ColoringApp/Features/ColoringPageGallery/ViewModels/ColoringPageGalleryViewModel.swift (uses ColoringPageRepository)
- [ ] T016 [US1] Implement loadAll() in ColoringPageRepository to load built-in pages from Bundle (depends on T012)
- [ ] T017 [US1] Add navigation from gallery to DrawingCanvasView using NavigationStack in ColoringPageGalleryView.swift
- [ ] T018 [US1] Create DrawingCanvasView (SwiftUI) in ColoringApp/Features/DrawingCanvas/Views/DrawingCanvasView.swift (displays coloring page as background)
- [ ] T019 [US1] Update ColoringAppApp.swift entry point to show ColoringPageGalleryView as root view

**Checkpoint**: At this point, User Story 1 should be fully functional and testable independently (can select and view pages)

---

## Phase 4: User Story 2 - Color with Basic Brushes (Priority: P1)

**Goal**: Child can draw colored strokes on canvas with finger or Apple Pencil, switch between 12+ colors

**Independent Test**: Load page → select red → draw → select blue → draw → see both colored strokes

### Implementation for User Story 2

- [ ] T020 [P] [US2] Create PKCanvasViewRepresentable (UIViewRepresentable wrapper) in ColoringApp/Features/DrawingCanvas/Views/PKCanvasViewRepresentable.swift (wraps PencilKit PKCanvasView)
- [ ] T021 [P] [US2] Create ColorPaletteView (SwiftUI) in ColoringApp/Features/BrushTools/Views/ColorPaletteView.swift (grid of 12 color buttons, 44x44pt touch targets)
- [ ] T022 [P] [US2] Create DrawingCanvasViewModel (ObservableObject) in ColoringApp/Features/DrawingCanvas/ViewModels/DrawingCanvasViewModel.swift (manages PKInkingTool state)
- [ ] T023 [US2] Integrate PKCanvasViewRepresentable into DrawingCanvasView to enable drawing (depends on T018, T020)
- [ ] T024 [US2] Wire color selection from ColorPaletteView to update PKInkingTool.color in DrawingCanvasViewModel (depends on T021, T022)
- [ ] T025 [US2] Add ColorPaletteView overlay to DrawingCanvasView for color selection UI
- [ ] T026 [US2] Configure PKCanvasView for touch and Apple Pencil input (allowsFingerDrawing = true, minimumZoomScale = 1.0)

**Checkpoint**: At this point, User Stories 1 AND 2 should both work independently (can select pages and color them)

---

## Phase 5: User Story 3 - Erase and Undo Mistakes (Priority: P1)

**Goal**: Child can erase strokes or undo/redo actions to fix mistakes

**Independent Test**: Draw strokes → tap eraser → erase → tap undo → stroke restored → tap redo → stroke removed again

### Implementation for User Story 3

- [ ] T027 [P] [US3] Add eraser button to ColorPaletteView that switches PKCanvasView.tool to PKEraserTool(.vector) in ColoringApp/Features/BrushTools/Views/ColorPaletteView.swift
- [ ] T028 [P] [US3] Add undo button to DrawingCanvasView toolbar that calls PKCanvasView.undoManager.undo() in ColoringApp/Features/DrawingCanvas/Views/DrawingCanvasView.swift
- [ ] T029 [P] [US3] Add redo button to DrawingCanvasView toolbar that calls PKCanvasView.undoManager.redo() in ColoringApp/Features/DrawingCanvas/Views/DrawingCanvasView.swift
- [ ] T030 [US3] Disable undo/redo buttons when canUndo/canRedo is false using @Published state in DrawingCanvasViewModel
- [ ] T031 [US3] Add visual indication for active tool (border/highlight) in ColorPaletteView when eraser selected

**Checkpoint**: All P1 user stories complete - MVP ready for testing on physical iPad

---

## Phase 6: User Story 4 - Save Completed Artwork (Priority: P2)

**Goal**: Child can save colored artwork, view gallery of saved art, reopen for continued editing

**Independent Test**: Color page → save → start new page → go to gallery → see saved thumbnail → tap → artwork loads with strokes intact

### Implementation for User Story 4

- [ ] T032 [P] [US4] Implement ArtworkRepository save() method in ColoringApp/Core/Storage/ArtworkRepository.swift (saves PKDrawing.dataRepresentation() + metadata JSON)
- [ ] T033 [P] [US4] Implement ArtworkRepository loadAllMetadata() method to list saved artworks in ColoringApp/Core/Storage/ArtworkRepository.swift
- [ ] T034 [P] [US4] Implement ArtworkRepository loadDrawing() method to load PKDrawing from file in ColoringApp/Core/Storage/ArtworkRepository.swift
- [ ] T035 [P] [US4] Create ArtworkGalleryView (SwiftUI) in ColoringApp/Features/ArtworkGallery/Views/ArtworkGalleryView.swift (LazyVGrid with saved artwork thumbnails)
- [ ] T036 [P] [US4] Create ArtworkGalleryViewModel (ObservableObject) in ColoringApp/Features/ArtworkGallery/ViewModels/ArtworkGalleryViewModel.swift (uses ArtworkRepository)
- [ ] T037 [US4] Add save button to DrawingCanvasView toolbar that calls ArtworkRepository.save() in DrawingCanvasViewModel
- [ ] T038 [US4] Generate thumbnail using PKDrawing.image(from:scale:) when saving in DrawingCanvasViewModel
- [ ] T039 [US4] Add navigation tab/button to access ArtworkGalleryView from ColoringPageGalleryView
- [ ] T040 [US4] Implement loadDrawing() in DrawingCanvasViewModel to reopen saved artwork (sets PKCanvasView.drawing and background image)
- [ ] T041 [US4] Add auto-save on app backgrounding using scenePhase observer in DrawingCanvasView

**Checkpoint**: At this point, User Stories 1-4 complete - artwork persistence working

---

## Phase 7: User Story 5 - Add Custom Coloring Pages (Priority: P2)

**Goal**: Parent can import custom coloring page images from photo library

**Independent Test**: Tap add button → select photo → see it in gallery → select → color it

### Implementation for User Story 5

- [ ] T042 [P] [US5] Create PhotoPickerView (UIViewControllerRepresentable wrapper) in ColoringApp/Features/ColoringPageGallery/Views/PhotoPickerView.swift (wraps PHPickerViewController)
- [ ] T043 [P] [US5] Create ImageProcessor protocol and implementation in ColoringApp/Core/Utilities/ImageProcessor.swift per contracts/ README.md Section 4 (scale, validate, optimize images)
- [ ] T044 [US5] Implement importCustomPage() in ColoringPageRepository to save imported images to Documents/coloring-pages/ in ColoringApp/Core/Storage/ColoringPageRepository.swift
- [ ] T045 [US5] Add "Add Coloring Page" button to ColoringPageGalleryView that presents PhotoPickerView
- [ ] T046 [US5] Wire PhotoPickerView selection to call ColoringPageRepository.importCustomPage() in ColoringPageGalleryViewModel
- [ ] T047 [US5] Update ColoringPageRepository.loadAll() to include custom pages from Documents/ in addition to built-in pages

**Checkpoint**: At this point, User Stories 1-5 complete - custom page import working

---

## Phase 8: User Story 6 - Use Fun Special Brushes (Priority: P3)

**Goal**: Child can use rainbow and sparkle brush effects for creative variety

**Independent Test**: Select rainbow → draw → see gradient colors → select sparkle → draw → see glitter effect

### Implementation for User Story 6

- [ ] T048 [P] [US6] Add BrushType enum cases for rainbow and sparkle to Brush.swift in ColoringApp/Features/BrushTools/Models/Brush.swift
- [ ] T049 [P] [US6] Add rainbow and sparkle buttons to ColorPaletteView in ColoringApp/Features/BrushTools/Views/ColorPaletteView.swift
- [ ] T050 [US6] Implement rainbow brush effect (cycle colors along stroke path) in DrawingCanvasViewModel custom rendering logic
- [ ] T051 [US6] Implement sparkle brush effect (CAEmitterLayer particles) in DrawingCanvasView overlay
- [ ] T052 [US6] Test special brush performance at 60fps minimum on iPad Simulator and physical device

**Checkpoint**: At this point, User Stories 1-6 complete - special brushes working

---

## Phase 9: User Story 7 - Start Fresh with New Page (Priority: P3)

**Goal**: Child can navigate back to gallery and start new page with save prompt for unsaved work

**Independent Test**: Color page (don't save) → tap back → see save prompt → choose save/discard/cancel → verify correct behavior

### Implementation for User Story 7

- [ ] T053 [P] [US7] Add back/home button to DrawingCanvasView navigation bar that triggers navigation back to ColoringPageGalleryView
- [ ] T054 [US7] Implement unsaved changes detection by comparing current PKDrawing hash with last saved version in DrawingCanvasViewModel
- [ ] T055 [US7] Add Alert with "Save", "Discard", "Cancel" options when navigating away with unsaved changes in DrawingCanvasView
- [ ] T056 [US7] Wire "Save" option to call ArtworkRepository.save() before dismissing in DrawingCanvasViewModel

**Checkpoint**: All user stories complete - full feature set implemented

---

## Phase 10: Polish & Cross-Cutting Concerns

**Purpose**: Improvements that affect multiple user stories

- [ ] T057 [P] Add app icon and launch screen in ColoringApp/Resources/Assets.xcassets/
- [ ] T058 [P] Implement accessibility labels (VoiceOver support) for all interactive elements across views
- [ ] T059 [P] Add haptic feedback for button taps using UIImpactFeedbackGenerator in appropriate views
- [ ] T060 [P] Optimize memory usage by implementing lazy loading for gallery thumbnails
- [ ] T061 [P] Add error alerts for storage failures (show StorageError messages) across all ViewModels
- [ ] T062 Test on physical iPad with Apple Pencil to verify <20ms latency per quickstart.md Section 7
- [ ] T063 Profile with Instruments to verify 60fps drawing performance and <150MB memory usage per quickstart.md Section 11
- [ ] T064 Run quickstart.md validation checklist for all P1/P2 features

---

## Dependencies & Execution Order

### Phase Dependencies

- **Setup (Phase 1)**: No dependencies - can start immediately (requires Mac with Xcode)
- **Foundational (Phase 2)**: Depends on Setup completion - BLOCKS all user stories
- **User Stories (Phase 3-9)**: All depend on Foundational phase completion
  - User stories can proceed in parallel (if multiple developers) or sequentially by priority
  - P1 stories (US1-3) should be completed first for MVP
  - P2 stories (US4-5) add persistence and customization
  - P3 stories (US6-7) add polish features
- **Polish (Phase 10)**: Depends on all desired user stories being complete

### User Story Dependencies

- **User Story 1 (P1)**: Can start after Foundational (Phase 2) - No dependencies on other stories
- **User Story 2 (P1)**: Depends on US1 (needs DrawingCanvasView from T018) - Integrates drawing into existing canvas
- **User Story 3 (P1)**: Depends on US2 (needs PKCanvasView from T020) - Adds undo/redo to existing drawing
- **User Story 4 (P2)**: Depends on US2 (needs PKDrawing from drawing canvas) - Adds persistence
- **User Story 5 (P2)**: Can start after Foundational (Phase 2) - Independent (imports pages, doesn't need drawing)
- **User Story 6 (P3)**: Depends on US2 (extends brush system) - Adds special effects
- **User Story 7 (P3)**: Depends on US1 and US4 (needs navigation and save logic) - Adds workflow navigation

### Within Each User Story

- Tasks marked [P] can run in parallel (different files)
- Non-parallel tasks have dependencies (check "depends on TX" notes)
- Models before services
- Services before UI
- Core implementation before integration
- Story complete and tested before moving to next priority

### Parallel Opportunities

- All Setup tasks (T002, T003) can run in parallel with T001 once project created
- All Foundational model/protocol tasks (T005-T011) can run in parallel
- Within US1: T014 and T015 can run in parallel
- Within US2: T020, T021, T022 can all run in parallel
- Within US3: T027, T028, T029 can all run in parallel
- Within US4: T032, T033, T034, T035, T036 can all run in parallel
- Within US5: T042, T043 can run in parallel
- Within US6: T048, T049 can run in parallel
- Within US7: T053, T054 can run in parallel
- All Polish tasks (T057-T061) can run in parallel

---

## Parallel Example: User Story 2

```bash
# Launch all model/view tasks for User Story 2 together:
Task T020: "Create PKCanvasViewRepresentable in ColoringApp/Features/DrawingCanvas/Views/PKCanvasViewRepresentable.swift"
Task T021: "Create ColorPaletteView in ColoringApp/Features/BrushTools/Views/ColorPaletteView.swift"
Task T022: "Create DrawingCanvasViewModel in ColoringApp/Features/DrawingCanvas/ViewModels/DrawingCanvasViewModel.swift"

# Then integration tasks sequentially (T023-T026) as they depend on the parallel tasks
```

---

## Implementation Strategy

### MVP First (User Stories 1-3 Only)

1. Complete Phase 1: Setup (on Mac with Xcode)
2. Complete Phase 2: Foundational (CRITICAL - blocks all stories)
3. Complete Phase 3: User Story 1 (gallery and page selection)
4. Complete Phase 4: User Story 2 (drawing with colors)
5. Complete Phase 5: User Story 3 (erase and undo)
6. **STOP and VALIDATE**: Test all P1 stories on physical iPad with Apple Pencil
7. Deploy to household iPads via sideloading or TestFlight

### Incremental Delivery

1. Complete Setup + Foundational → Foundation ready
2. Add US1 → Test independently → Demo to family
3. Add US2 → Test independently → Kids can color! (MVP!)
4. Add US3 → Test independently → Mistake correction working
5. Add US4-5 → Test independently → Persistence and custom pages
6. Add US6-7 → Test independently → Full feature set
7. Each story adds value without breaking previous stories

### Parallel Team Strategy

With hybrid Windows PC (code drafting) + MacBook Pro (building/testing) workflow:

1. Complete Setup + Foundational on Mac
2. Draft implementation code on Windows PC using VS Code + GitHub Copilot
3. Commit code to GitHub
4. Pull and test on Mac with Xcode + iPad Simulator
5. Fix compilation errors and test on physical iPad
6. Iterate between Windows drafting and Mac validation

---

## Summary

**Total Tasks**: 64

- **Phase 1 (Setup)**: 4 tasks
- **Phase 2 (Foundational)**: 9 tasks
- **Phase 3 (US1 - P1)**: 6 tasks
- **Phase 4 (US2 - P1)**: 7 tasks
- **Phase 5 (US3 - P1)**: 5 tasks
- **Phase 6 (US4 - P2)**: 10 tasks
- **Phase 7 (US5 - P2)**: 6 tasks
- **Phase 8 (US6 - P3)**: 5 tasks
- **Phase 9 (US7 - P3)**: 4 tasks
- **Phase 10 (Polish)**: 8 tasks

**Parallel Opportunities**: 24 tasks marked [P] across all phases

**MVP Scope**: Phases 1-5 (User Stories 1-3 = 31 tasks total) delivers core coloring experience

**Estimated Timeline** (solo developer, part-time per plan.md):

- MVP (P1 stories): 2-3 weeks
- Full P1+P2: 4-5 weeks
- All features: 5-6 weeks

**Next Step**: Begin T001 on MacBook Pro following quickstart.md
