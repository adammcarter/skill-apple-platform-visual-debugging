# SwiftUI Investigation

Use this when the visible bug is tied to SwiftUI identity, invalidation, branching, subtree replacement, or transition ownership.

## Core Bias

- SwiftUI bugs often come from identity and ownership, not visual modifiers alone.
- Prove whether the subtree is stable before optimizing the transition or styling.
- Treat `if` branches, `id(...)`, and view replacement as visual debugging suspects.

## Identity and Lifetime

Probe:

- `onAppear` and `onDisappear`
- stable IDs
- conditional branches that replace one subtree with another
- `id(...)` usage
- `matchedGeometryEffect` IDs and namespace ownership
- `Self._printChanges()`

Questions:

- Is the view being recreated unexpectedly?
- Is a branch flip tearing down a subtree?
- Is an `id` forcing a reset?
- Is `matchedGeometryEffect` failing because one endpoint disappears too early?

## Animation and Transition Timing

Probe:

- phase/state transitions
- whether old and new views coexist temporarily
- clipping, masking, and z-order ownership during the transition
- whether animation should be disabled locally to isolate the seam

When the user needs proof, prefer visual overlays and a small screenshot set over dense logs.

## State Ownership

Trace the true owner of the state that moves the UI.

If several wrappers forward the same state, log at the mutation boundary instead of every consumer.

Common traps:

- one visual state derived from several booleans that can drift apart
- local `@State` stored in a subtree that is being recreated
- container-level animation causing a wider subtree to animate than intended

## Anti-Patterns

- adding animation modifiers everywhere before proving the timing issue
- assuming `onAppear` means "created once"
- forcing new IDs as a workaround before understanding why the view resets
