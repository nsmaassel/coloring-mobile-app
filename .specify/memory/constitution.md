<!--
Sync Impact Report:
Version Change: Initial → 1.1.0
Modified Principles:
  - II. Apple Platform Standards: Removed prescriptive technology choices (SwiftUI/UIKit/PencilKit), 
    replaced with research-first approach. Technology decisions now emerge from research phase.
  - III. Learning-Driven Development: Enhanced to emphasize "learning project first" mindset,
    added technology evaluation and "what I learned" documentation requirements.
Added Sections:
  - Core Principles (5 principles: User-First Design, Apple Platform Standards, Learning-Driven Development, Feature Modularity, Performance & Responsiveness)
  - Quality Standards
  - Development Workflow (enhanced Research phase for first-time iOS developers)
  - Governance
Removed Sections: N/A
Templates Status:
  ✅ plan-template.md - Constitution Check gate validated, aligns with principles. Research phase supports technology exploration.
  ✅ spec-template.md - User story prioritization aligns with User-First Design principle
  ✅ tasks-template.md - Modular task structure supports Feature Modularity principle
  ⚠ No command-specific templates found in .specify/templates/commands/
Follow-up TODOs: None
Rationale for MINOR bump: Added material guidance for research-driven technology selection and enhanced learning documentation requirements without removing existing principles.
-->

# Coloring & Drawing Studio Constitution

## Core Principles

### I. User-First Design

Every feature MUST be designed for young children (ages 3-8) with iPad and Apple Pencil as the primary interaction methods. Features MUST be:

- Intuitive enough for pre-readers to understand through visual cues and icons
- Touch-friendly with appropriately sized interactive areas (minimum 44x44 points)
- Forgiving of mistakes with easy undo/redo capabilities
- Delightful with appropriate animations and feedback

**Rationale**: The target audience has limited fine motor skills, reading ability, and patience. Interface complexity or unclear interactions will immediately lead to frustration and app abandonment. Every design decision must pass the "can a 4-year-old use this without instruction?" test.

### II. Apple Platform Standards

All development MUST follow Apple's Human Interface Guidelines and leverage native iOS frameworks. Technology choices MUST be:

- Researched and justified through Apple's official documentation and resources
- Selected based on suitability for the specific feature requirements
- Evaluated for learning value and industry relevance
- Aligned with Apple's recommended best practices for the use case
- Documented with reasoning for future reference

Code MUST:

- Follow iOS design patterns (navigation, gestures, accessibility)
- Support iPad-specific features appropriate to the app's functionality
- Integrate with iOS accessibility features (VoiceOver, Dynamic Type, Reduce Motion)

**Rationale**: This project serves as a learning vehicle for iOS development. Technology decisions (SwiftUI vs UIKit, PencilKit vs custom drawing, etc.) should emerge from research rather than assumptions. Adhering to Apple's standards teaches industry best practices and ensures the app feels native, while the research process itself builds understanding of the ecosystem.

### III. Learning-Driven Development

Development approach MUST prioritize understanding over speed. This is a learning project first, a shipping product second. Every implementation MUST:

- Be preceded by research into iOS best practices and relevant Apple documentation
- Include technology evaluation when multiple approaches exist (document trade-offs)
- Capture "what I learned" notes in feature research documents
- Include inline comments explaining WHY decisions were made, not just WHAT the code does
- Document challenges encountered and how they were resolved
- Prefer readable, maintainable code over clever optimizations unless performance requires it
- Include references to Apple documentation, WWDC sessions, or learning resources consulted

**Rationale**: You've never built an iOS app before—this is your chance to learn properly. Rushing to copy-paste solutions without understanding undermines the primary goal. The spec and research documents become your learning journal, valuable for both completing this project and building future iOS apps. SpecKit's research phase is specifically designed to support this exploratory learning approach.

### IV. Feature Modularity

Features MUST be implemented as independent, self-contained modules that can be developed, tested, and refined in isolation. Each feature MUST:

- Have clearly defined boundaries and minimal dependencies on other features
- Be capable of functioning independently for testing purposes
- Have its own documentation, acceptance criteria, and test scenarios
- Follow a consistent architecture pattern across all modules

**Rationale**: Modular features enable incremental learning and development. You can fully understand one feature domain (e.g., drawing tools) before moving to another (e.g., color selection). Modularity also allows features to be built in priority order without blocking dependencies.

### V. Performance & Responsiveness

Drawing and interaction MUST feel immediate and natural. Performance requirements:

- Apple Pencil input latency MUST be <20ms for visible feedback
- Touch interactions MUST respond within 100ms
- UI animations MUST run at 60fps minimum (120fps on ProMotion displays)
- App launch MUST complete within 2 seconds
- Memory usage MUST stay under 150MB during normal operation

**Rationale**: Drawing apps live or die by their responsiveness. Laggy drawing destroys the creative experience and makes the app unusable for children. Performance is non-negotiable and must be validated throughout development, not optimized afterward.

## Quality Standards

### Child Safety & Privacy

- NO network connectivity (app operates entirely offline)
- NO data collection, analytics, or tracking of any kind
- NO in-app purchases or advertisements
- NO external content loading or web views
- MUST comply with COPPA and children's privacy best practices

### Accessibility Requirements

- VoiceOver support for all interactive elements
- Dynamic Type support for any text content
- High contrast and color-blind friendly color schemes
- Support for accessibility settings (Reduce Motion, Increase Contrast)
- Alternative navigation methods beyond touch (keyboard, pointer)

### Testing Standards

- Simulator testing for all features during development (primary testing environment as you learn)
- Physical iPad testing when available, especially for Apple Pencil and performance validation
- Manual verification of acceptance criteria for each user story
- Performance profiling using Xcode's built-in tools when performance issues are suspected
- Accessibility audit using Xcode Accessibility Inspector as features are completed
- Document testing approach and any testing tools/frameworks discovered during research

## Development Workflow

### Feature Development Cycle

1. **Specification**: Define user stories with acceptance criteria and priority levels (P1/P2/P3)
2. **Research** (CRITICAL for learning):
   - Identify what you need to learn to implement the feature
   - Study relevant iOS APIs and frameworks
   - Read Apple documentation and Human Interface Guidelines
   - Review WWDC sessions related to the feature domain
   - Document technology options and trade-offs
   - Capture learning notes and open questions
3. **Design**: Create modular architecture plan based on research findings
4. **Implementation**: Build feature following selected approach with comprehensive commenting
5. **Testing**: Manual validation on device (when possible) or simulator, verify acceptance criteria
6. **Documentation**: Update feature docs with what you learned and implementation notes
7. **Refinement**: Iterate based on testing findings before moving to next feature

### Priority Execution

- P1 features are MVP requirements and MUST be completed before P2 work begins
- Each priority level MUST be fully functional and tested independently
- Features within the same priority level can be developed in parallel
- No priority level is "done" until all its features pass testing and documentation gates

### Version Control

- Feature branches named `###-feature-name` for all development work
- Commits MUST be atomic (one logical change per commit)
- Commit messages MUST explain the "why" when not obvious
- Main branch MUST always contain working, tested code

## Governance

This constitution defines the non-negotiable principles and standards for the Coloring & Drawing Studio project. All development activities, feature specifications, and implementation plans MUST align with these principles.

### Amendment Process

- Amendments require clear justification of why current principles are insufficient
- Version number MUST be incremented following semantic versioning (MAJOR.MINOR.PATCH)
- All templates and dependent documentation MUST be updated to reflect amendments
- Learning insights that challenge principles should be documented and may trigger amendments

### Compliance & Reviews

- All feature specifications MUST pass constitution alignment check before planning begins
- Implementation plans MUST include a "Constitution Check" section addressing each principle
- Code reviews MUST verify adherence to Apple Platform Standards and code quality principles
- Performance requirements MUST be validated before features are considered complete

### Complexity Justification

Any violation of principles (e.g., introducing cross-feature dependencies, deviating from Apple standards) MUST be explicitly justified in planning documents with:

- Specific reason the principle cannot be followed
- Alternative approaches considered and why they were rejected
- Mitigation strategy to minimize negative impact
- Plan to refactor toward compliance when constraints are removed

**Version**: 1.1.0 | **Ratified**: 2025-11-09 | **Last Amended**: 2025-11-09
