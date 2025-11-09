# GitHub Copilot Agents

This directory contains specialized agent configurations for GitHub Copilot to assist with iOS development tasks for the Coloring & Drawing Studio project.

## Available Agents

### 🎯 [ios-developer.md](./ios-developer.md)
**General iOS development expert**
- Swift 5.9+ language features and patterns
- iOS frameworks (UIKit, SwiftUI, PencilKit, PhotoKit)
- iPad-specific features and optimizations
- Xcode project configuration
- Performance profiling and optimization

**When to use**: General iOS coding tasks, framework integration, performance optimization, Xcode project setup.

---

### 🎨 [swiftui-expert.md](./swiftui-expert.md)
**SwiftUI UI design and implementation specialist**
- Declarative UI composition
- State management (@State, @Binding, @ObservedObject, etc.)
- Custom views and modifiers
- Layout and navigation
- Animations and transitions
- SwiftUI + UIKit bridging

**When to use**: Building SwiftUI views, state management questions, layout challenges, custom view components, accessibility implementation.

---

### ✏️ [pencilkit-specialist.md](./pencilkit-specialist.md)
**Apple Pencil drawing and PencilKit expert**
- PKCanvasView setup and configuration
- Apple Pencil pressure and tilt handling
- Low-latency drawing optimization (<20ms target)
- Brush customization and effects
- Drawing serialization and thumbnails
- Performance profiling for drawing

**When to use**: Implementing drawing canvas, Apple Pencil integration, brush tools, undo/redo, saving/loading drawings, drawing performance optimization.

---

### 🧪 [testing-specialist.md](./testing-specialist.md)
**iOS testing expert (XCTest, UI testing, TDD)**
- Unit testing with XCTest
- UI testing with XCUITest
- Testing async/await code
- Mock objects and test doubles
- Performance testing
- Test organization and best practices

**When to use**: Writing unit tests, UI automation tests, testing async code, creating mocks, performance testing, test coverage analysis.

---

### 🏗️ [architecture-guide.md](./architecture-guide.md)
**Architecture patterns and code organization**
- MVVM architecture pattern
- Protocol-oriented programming
- Dependency injection patterns
- Feature modularity
- Error handling strategies
- Code organization best practices

**When to use**: Designing feature architecture, structuring code modules, implementing repositories, setting up dependency injection, organizing project structure.

---

## How to Use These Agents

### In GitHub Copilot Chat

When working on a specific task, you can reference these agents to get contextually-aware assistance:

1. **Open GitHub Copilot Chat** (Cmd+Shift+I in VS Code or Xcode)

2. **Reference an agent** by mentioning the file:
   ```
   Using the ios-developer agent guidance, help me implement a color palette view
   ```

3. **Ask specific questions**:
   ```
   @swiftui-expert How should I handle state management for a drawing canvas?
   ```

4. **Get architecture advice**:
   ```
   Following the architecture-guide, how should I structure a new feature module?
   ```

### Agent Responsibilities

Each agent has specific expertise areas and should be consulted for their domain:

```
Task: Implement color selection UI
→ Use: swiftui-expert.md (UI design + state management)

Task: Add Apple Pencil drawing
→ Use: pencilkit-specialist.md (PencilKit integration)

Task: Create repository for saving artwork
→ Use: architecture-guide.md (repository pattern design)

Task: Write tests for color palette
→ Use: testing-specialist.md (test structure and best practices)

Task: Optimize drawing performance
→ Use: pencilkit-specialist.md + ios-developer.md (profiling and optimization)
```

## Agent Coordination

For complex tasks that span multiple domains, coordinate between agents:

### Example: Implementing Drawing Canvas

1. **Architecture** (architecture-guide.md)
   - Design MVVM structure
   - Define repository protocols
   - Plan state management

2. **UI** (swiftui-expert.md)
   - Create SwiftUI wrapper view
   - Setup state bindings
   - Add toolbar and controls

3. **Drawing** (pencilkit-specialist.md)
   - Configure PKCanvasView
   - Implement brush tools
   - Optimize Apple Pencil latency

4. **Testing** (testing-specialist.md)
   - Write ViewModel tests
   - Create UI automation tests
   - Test performance benchmarks

## Project-Specific Context

All agents are configured with project-specific context:

### Target Audience
- Children ages 3-8
- iPad with Apple Pencil
- Touch-friendly interfaces (44x44 minimum)

### Performance Requirements
- Apple Pencil latency: <20ms
- Frame rate: 60fps minimum (120fps on ProMotion)
- App launch: <2 seconds
- Memory usage: <150MB

### Privacy & Safety
- Offline-only (no network)
- No data collection
- COPPA compliant
- Local storage only

### Learning Focus
- First-time iOS developer
- Educational comments in code
- References to Apple documentation
- Best practices and patterns

## Best Practices

### Do:
✅ Reference the appropriate agent for your task  
✅ Follow the code style guidelines in each agent  
✅ Use the examples as templates  
✅ Ask for explanations and learning resources  
✅ Coordinate multiple agents for complex features  

### Don't:
❌ Mix responsibilities (e.g., UI logic in models)  
❌ Skip testing recommendations  
❌ Ignore performance requirements  
❌ Violate child safety principles  
❌ Add network connectivity  

## Contributing to Agents

As you learn and discover better patterns, these agent files can be updated:

1. **Add new examples** based on real implementation
2. **Update best practices** with lessons learned
3. **Expand expertise areas** as new challenges arise
4. **Document pitfalls** to avoid for future reference

## Quick Reference

| Task | Primary Agent | Secondary Agent |
|------|---------------|-----------------|
| Design feature architecture | architecture-guide | - |
| Build SwiftUI view | swiftui-expert | ios-developer |
| Implement drawing canvas | pencilkit-specialist | swiftui-expert |
| Add Apple Pencil support | pencilkit-specialist | ios-developer |
| Write unit tests | testing-specialist | architecture-guide |
| Create repository | architecture-guide | ios-developer |
| Optimize performance | ios-developer | pencilkit-specialist |
| Design state management | swiftui-expert | architecture-guide |
| Setup project structure | ios-developer | architecture-guide |
| Implement accessibility | swiftui-expert | ios-developer |

---

**Remember**: These agents are designed to teach iOS development while building production-quality code. Don't hesitate to ask "why" and request explanations!
