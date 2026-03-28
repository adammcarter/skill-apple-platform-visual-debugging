# Trusted Resources

Consult authoritative Apple documentation and visual-debugging references when:

- the issue depends on framework behavior rather than app logic
- the same debugging loop has stalled more than twice
- the problem involves the Xcode view debugger, Instruments, rendering, transitions, or host-view behavior
- rendering behavior is unclear

## Primary Canonical Sources

1. Apple Developer Documentation
   - Use as the primary canonical source for framework behavior, API contracts, view debugging features, Instruments, and current Apple guidance.
   - Home: https://developer.apple.com/

2. Apple WWDC Sessions
   - Use when the challenge is not just API syntax but the intended debugging, performance, animation, or platform-specific UI workflow.
   - Home: https://developer.apple.com/videos/

3. Swift.org
   - Use for language semantics when they materially affect a visible UI conclusion.
   - Home: https://www.swift.org/

## Secondary Trusted Sources

4. objc.io
   - Use for deeper engineering guidance on SwiftUI, UIKit, AppKit, rendering, performance, and architecture.
   - Home: https://www.objc.io/

5. Point-Free
   - Use for disciplined reasoning about state, effects, testing, and SwiftUI behavior when the debugging problem is really a state-modeling problem.
   - Home: https://www.pointfree.co/

6. Hacking with Swift
   - Use for fast practical examples when you need a concrete Xcode, SwiftUI, or UIKit implementation pattern quickly.
   - Home: https://www.hackingwithswift.com/

## How To Use Them

- Start with Apple documentation and WWDC for API truth and intended platform direction.
- Use Swift.org when language behavior materially affects what becomes visible on screen.
- Use objc.io, Point-Free, and Hacking with Swift as secondary trusted sources for examples and engineering judgment when the canonical sources are thin or too abstract.
- If these sources disagree, prefer Apple documentation and Swift.org for factual behavior.
- If you have already taken two weak swings at the issue, stop improvising and consult one of these directly.
