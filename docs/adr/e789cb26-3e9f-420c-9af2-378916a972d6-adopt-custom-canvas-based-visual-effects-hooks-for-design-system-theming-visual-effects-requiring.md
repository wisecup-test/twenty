# Adopt Custom Canvas-Based Visual Effects Hooks for Design System Theming: Visual Effects Requiring

Status: proposed
Date: 2024-01-15
Deciders: Detection Pipeline (automated)

## Context

- The website requires sophisticated visual effects including halftone image backdrops, animated canvas elements (hourglass), and dynamic layout filters to create an engaging user experience
- Custom React hooks encapsulate complex canvas manipulation logic, separating visual effect implementation from component presentation logic
- The pattern emerged across multiple sections of the website (ThreeCards, Testimonials, FeatureCard) indicating a systematic approach to visual theming
- Canvas-based effects provide performance advantages over CSS-only solutions for complex animations and image processing
- The hooks follow React best practices by managing canvas lifecycle, animation frames, and cleanup within the hook abstraction

## Problem Statement

How should the design system implement advanced visual effects (halftone processing, canvas animations, dynamic layouts) in a way that is reusable, performant, maintainable, and consistent with React patterns while supporting the website's theming requirements?

## Decision

1. MUST: Visual effects requiring canvas manipulation MUST be implemented as custom React hooks with the naming convention 'use-[effect-name]'

## Policy Block

- MUST Visual effects requiring canvas manipulation MUST be implemented as custom React hooks with the naming convention 'use-[effect-name]'

In scope:
- Custom visual effects for website sections and feature cards
- Canvas-based animations and image processing effects
- Theming-related visual enhancements that require programmatic rendering
- Reusable effect logic that spans multiple components or sections

Out of scope:
- Simple CSS-based styling and transitions
- Third-party animation libraries or effect frameworks
- Server-side image processing or manipulation
- Non-visual React hooks (data fetching, state management, etc.)

## Rationale

- The pattern appears in 3 distinct files across different website sections with 90.90% confidence, indicating a deliberate architectural choice rather than isolated implementation
- Custom hooks provide excellent separation of concerns by isolating complex canvas logic from component rendering, improving testability and maintainability
- Canvas-based effects offer superior performance for complex visual manipulations (halftone processing, animated graphics) compared to DOM-based alternatives
- The colocation strategy (hooks within component directories) keeps related code together while maintaining clear boundaries between effect logic and presentation

## Consequences

Positive:
- Reusable visual effect logic that can be shared across multiple components without duplication
- Clear separation between canvas manipulation logic and React component rendering concerns
- Performance optimization through canvas APIs for complex visual effects that would be expensive in CSS/DOM
- Consistent patterns for implementing new visual effects across the design system
- Easier testing of visual effect logic in isolation from component structure

Negative:
- Increased complexity compared to simple CSS-based solutions for basic visual effects
- Requires developers to understand both React hooks patterns and canvas APIs
- Canvas-based effects may have accessibility challenges that need additional consideration
- Potential for inconsistent implementations if hook patterns are not well-documented

## Alternatives

- Use CSS-only solutions with animations and filters for all visual effects (rejected)
  Rejected because: CSS lacks the programmatic control needed for complex effects like halftone processing and dynamic canvas animations; performance limitations for image manipulation
  When valid: For simple transitions, transforms, and filters that don't require frame-by-frame control
- Adopt a third-party animation library (Framer Motion, GSAP, Three.js) (rejected)
  Rejected because: Adds significant bundle size and external dependencies; may not provide the specific low-level canvas control needed for custom effects
  When valid: For complex 3D graphics or when standardized animation patterns are sufficient
- Implement effects as standalone utility functions rather than hooks (rejected)
  Rejected because: Loses React lifecycle integration, making cleanup and state management more error-prone; doesn't leverage React's component model
  When valid: For pure computational functions that don't need lifecycle management or React state

## Risks

- Canvas-based effects may not be accessible to screen readers or users with visual impairments
  Mitigation: Ensure all canvas effects are decorative only or provide equivalent ARIA labels and semantic HTML alternatives
  Owner: Frontend Engineering Team
- Performance degradation on low-end devices or browsers with poor canvas performance
  Mitigation: Implement performance monitoring, provide fallback options, and use feature detection to disable effects on constrained devices
  Owner: Frontend Engineering Team
- Inconsistent hook implementations across different sections leading to maintenance burden
  Mitigation: Create shared base utilities for common canvas operations, document hook patterns, and conduct code reviews for new effect hooks
  Owner: Design System Team

## Implementation Notes

- Create a shared utilities module for common canvas operations (context setup, animation frame management, cleanup patterns) to reduce duplication
- Document the standard hook structure including useEffect for lifecycle, useRef for canvas elements, and cleanup patterns in a design system guide
- Consider creating a base hook or helper functions for requestAnimationFrame management to ensure consistent animation patterns
- Establish naming conventions: 'use-[effect-name]' for hooks, colocate in 'hooks/' subdirectories within component folders
- Add TypeScript types for hook return values and configuration parameters to improve developer experience

## Continuation Context


Verify commands:
- find . -path '*/hooks/use-*.ts' -type f | grep -E '(halftone|canvas|layout|effect)' | wc -l
- grep -r 'requestAnimationFrame\|canvas\.getContext' --include='use-*.ts' | wc -l
- grep -r 'useEffect.*return.*=>.*cancel' --include='use-*.ts' packages/twenty-website-new/src/sections/ | wc -l

Accept when:
- At least 3 custom effect hooks exist following the 'use-[effect-name]' naming pattern in the website sections
- All canvas-based effect hooks properly implement cleanup logic using useEffect return functions
- Effect hooks are colocated within 'hooks/' subdirectories of their consuming components or sections

## Enforcement

- Verified by: Code review process checking for proper hook patterns and lifecycle management
- Verified by: Automated linting rules for hook naming conventions and file organization
- Verified by: Visual regression testing to ensure effects render consistently across browsers
- Violation handling: Code review feedback requesting refactoring to follow hook patterns
- Violation handling: Linter warnings for hooks not following naming conventions or colocation rules
- Violation handling: Performance monitoring alerts for canvas effects causing frame drops
- Exception process: Document rationale for alternative approach in component comments or ADR
- Exception process: Obtain approval from design system team lead for deviations from standard patterns
- Exception process: Create follow-up task to refactor to standard pattern if exception is temporary