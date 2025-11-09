# Using GitHub Copilot Agents

This guide explains how to effectively use the specialized GitHub Copilot agents configured for this project.

## What are Copilot Agents?

GitHub Copilot agents are specialized AI assistants with domain-specific expertise. This project includes five agents, each expert in different aspects of iOS development:

1. **iOS Developer** - General iOS development
2. **SwiftUI Expert** - UI design and state management
3. **PencilKit Specialist** - Apple Pencil and drawing
4. **Testing Specialist** - Testing and TDD
5. **Architecture Guide** - Code organization and patterns

## Quick Start

### In VS Code

1. Open GitHub Copilot Chat (Ctrl+Shift+I or Cmd+Shift+I)

2. Reference an agent file in your question:
   ```
   Using the swiftui-expert agent, help me create a color palette view
   ```

3. Or ask for specific domain help:
   ```
   @swiftui-expert How should I manage state for a drawing canvas?
   ```

### In Xcode

1. Open Copilot for Xcode chat interface

2. Reference the agent documentation:
   ```
   Following the pencilkit-specialist guidelines, help me optimize drawing performance
   ```

## Common Workflows

### Building a New Feature

**Step 1: Architecture Planning**
```
Using the architecture-guide agent, help me design the MVVM structure for 
a color palette feature. I need models, views, viewmodels, and repositories.
```

**Step 2: UI Implementation**
```
Using the swiftui-expert agent, help me implement the ColorPaletteView. 
It should display 12 colors in a grid with the selected color highlighted.
```

**Step 3: Writing Tests**
```
Using the testing-specialist agent, help me write unit tests for 
ColorPaletteViewModel including test cases for color selection.
```

### Solving Specific Problems

**Performance Issue**
```
@pencilkit-specialist I'm experiencing drawing latency of about 50ms. 
How can I optimize to reach the <20ms target?
```

**State Management Question**
```
@swiftui-expert Should I use @State or @Binding for the selected color 
in a child view that needs to update the parent's state?
```

**Testing Async Code**
```
@testing-specialist How do I write tests for async/await functions 
in my ViewModel that load data from a repository?
```

## Agent Selection Guide

| Task | Use This Agent | Why |
|------|---------------|-----|
| Designing feature structure | architecture-guide | Knows MVVM, protocols, DI patterns |
| Building SwiftUI view | swiftui-expert | Specializes in declarative UI |
| Drawing implementation | pencilkit-specialist | Experts in PencilKit and Apple Pencil |
| Writing tests | testing-specialist | Knows XCTest patterns |
| General iOS question | ios-developer | Broad iOS knowledge |
| Xcode configuration | ios-developer | Familiar with Xcode setup |
| State management | swiftui-expert | Understands @State, @Binding, etc. |
| Performance tuning | pencilkit-specialist + ios-developer | Combined expertise |
| Code organization | architecture-guide | Knows project structure |

## Multi-Agent Coordination

For complex features, coordinate multiple agents:

### Example: Implementing Drawing Canvas

**Step 1: Architecture (architecture-guide)**
```
Help me design the architecture for a drawing canvas feature:
- What models do I need?
- How should the ViewModel be structured?
- What protocols should I define?
```

**Step 2: PencilKit Setup (pencilkit-specialist)**
```
Help me implement the PKCanvasView configuration:
- Setup for Apple Pencil support
- Configure for <20ms latency
- Implement brush tools
```

**Step 3: SwiftUI Integration (swiftui-expert)**
```
Help me create the SwiftUI wrapper for PKCanvasView:
- UIViewRepresentable implementation
- State binding for selected color
- Coordinator pattern
```

**Step 4: Testing (testing-specialist)**
```
Help me write tests for the drawing canvas:
- Unit tests for ViewModel
- Mock the PencilKit components
- Test state management
```

## Best Practices

### Do:
✅ **Be specific** - Mention the exact feature or problem  
✅ **Provide context** - Explain what you've tried  
✅ **Reference agents** - Use their domain expertise  
✅ **Ask for explanations** - Learn the "why" not just "how"  
✅ **Request examples** - Code samples help understanding  

### Don't:
❌ **Mix concerns** - Use the right agent for the task  
❌ **Ignore project context** - Agents know our requirements  
❌ **Skip learning** - Ask for explanations, not just code  
❌ **Forget testing** - Always consult testing-specialist  
❌ **Violate principles** - Agents follow our constitution  

## Example Conversations

### Designing a Feature

**You:**
```
@architecture-guide I need to implement a feature for saving completed artwork. 
How should I structure this following MVVM pattern?
```

**Agent Response:**
```
For artwork saving, I recommend this MVVM structure:

Model:
- Artwork (Codable, Identifiable)
  - id, coloringPageId, drawing (PKDrawing), createdAt, thumbnail

Repository Protocol:
- ArtworkRepositoryProtocol
  - save(artwork: Artwork) async throws
  - loadAll() async throws -> [Artwork]
  - delete(id: String) async throws

ViewModel:
- ArtworkGalleryViewModel (@MainActor, ObservableObject)
  - @Published var artworks: [Artwork]
  - @Published var isLoading: Bool
  - func loadArtwork() async
  - func saveArtwork(PKDrawing, ColoringPage) async

Repository Implementation:
- ArtworkRepository (file-based storage)
  - Uses FileManager for persistence
  - Codable for serialization

This keeps concerns separated and testable...
```

### Solving a Bug

**You:**
```
@pencilkit-specialist My drawing strokes are appearing with 100ms delay. 
The target is <20ms. What's wrong?
```

**Agent Response:**
```
100ms latency suggests several potential issues:

1. Check drawingPolicy:
   canvasView.drawingPolicy = .anyInput // Should be .pencilOnly for best latency
   
2. Verify view hierarchy:
   - Canvas should be as flat as possible
   - Avoid heavy views underneath
   
3. Enable opaque rendering:
   canvasView.isOpaque = true
   canvasView.backgroundColor = .white
   
4. Profile with Instruments:
   - Use Core Animation instrument
   - Look for main thread blocking...
```

### Learning a Concept

**You:**
```
@swiftui-expert I'm confused about when to use @State vs @StateObject 
vs @ObservedObject. Can you explain with examples from our app?
```

**Agent Response:**
```
Great question! Here's when to use each in our coloring app:

@State - For simple, view-local values:
struct ColorPaletteView: View {
    @State private var selectedColor: Color = .red  // View-owned state
    ...
}

@StateObject - For creating ViewModels owned by the view:
struct ColoringPageGalleryView: View {
    @StateObject private var viewModel = ColoringPageGalleryViewModel()
    // View creates and owns this ViewModel
}

@ObservedObject - For ViewModels passed from parent:
struct ColoringPageCard: View {
    @ObservedObject var viewModel: ColoringPageViewModel
    // Parent owns, this view observes
}

Rule of thumb: Use @StateObject when you create the object, 
@ObservedObject when it's passed to you...
```

## Tips for Effective Use

### 1. Start Broad, Then Narrow
```
First: "Help me understand the architecture for saving artwork"
Then: "How do I implement the save() function in ArtworkRepository?"
Finally: "Why am I getting this encoding error?"
```

### 2. Share Your Code
```
I have this ViewModel:
[paste code]

@swiftui-expert How should I refactor this to follow best practices?
```

### 3. Ask "Why" Questions
```
@architecture-guide Why do we use protocols for repositories 
instead of concrete classes?
```

### 4. Request Comparisons
```
@ios-developer Should I use SwiftUI or UIKit for the drawing canvas? 
What are the trade-offs for our use case?
```

### 5. Seek Debugging Help
```
@testing-specialist My test is failing:
[paste test code]
[paste error]

What's wrong with my test setup?
```

## Project-Specific Context

All agents are aware of:

- **Target**: iPad app for children ages 3-8
- **Stack**: Swift 5.9+, SwiftUI, PencilKit
- **Performance**: <20ms latency, 60fps minimum
- **Privacy**: Offline-only, no data collection
- **Learning**: First-time iOS developer

You don't need to repeat this context in every question!

## Advanced Techniques

### Compare Approaches
```
@swiftui-expert Show me two different ways to handle color selection state:
one using @Binding and one using a callback closure. Which is better for our app?
```

### Request Refactoring
```
@architecture-guide Review this code and suggest improvements:
[paste code]

Focus on testability and maintainability.
```

### Deep Dive Learning
```
@pencilkit-specialist I want to understand PKCanvasView's rendering pipeline. 
Explain how strokes are converted from touch input to screen pixels, 
and where the 20ms latency comes from.
```

## Troubleshooting

### Agent Not Responding as Expected

1. **Be more specific**: Instead of "help with drawing", try "help me configure PKCanvasView for optimal Apple Pencil latency"

2. **Reference explicitly**: Use `@agent-name` or "Using the [agent] agent"

3. **Provide context**: Share relevant code, error messages, or what you've tried

4. **Check the docs**: Agents have extensive examples in their files

### Getting Generic Responses

If responses seem too generic or not project-specific:

```
I need help specific to our iPad coloring app for children. 
Please consider our performance requirements and child-friendly UX.
```

### Multiple Valid Approaches

When agents suggest different approaches:

```
@architecture-guide What's the recommended approach for our learning-focused project?
Consider both what's best practice and what's best for understanding iOS patterns.
```

## Next Steps

1. **Explore agent files**: Read [.github/agents/README.md](../.github/agents/README.md)
2. **Try examples**: Pick a task and consult the relevant agent
3. **Iterate**: Refine your questions based on responses
4. **Learn**: Focus on understanding, not just copying code
5. **Contribute**: Update agents with new learnings

Remember: These agents are here to teach you iOS development while building a production app. Don't hesitate to ask "why" and request explanations!

---

**Questions?** Check the individual agent files in `.github/agents/` for detailed expertise areas and examples.
