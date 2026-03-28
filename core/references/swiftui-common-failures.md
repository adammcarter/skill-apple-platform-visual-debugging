# SwiftUI Common Failures

Use this reference when the bug looks like a common SwiftUI visual trap rather than raw geometry math alone.

## Identity And State Traps

- `if` and `switch` branches can replace a subtree and reset local `@State`.
- `.id(...)` forces teardown and recreation. It is a common accidental cause of flicker, transition reset, and state loss.
- `ForEach` with unstable IDs causes lifetime churn that looks like a rendering bug.
- `matchedGeometryEffect` needs stable IDs, a shared namespace, and both endpoints to exist long enough for the handoff to work.

Questions to answer:

- Is the view actually the same visual owner, or a replacement?
- Did a branch flip or ID change recreate the subtree?
- Is the animation bug really a state-ownership bug?

## Invisible But Interactive Views

- `opacity(0)` hides pixels but does not disable hit testing.
- `Color.clear` overlays still own space and can still intercept taps when combined with gestures or `contentShape`.
- `.hidden()` preserves layout space. It is not the same as removing the view.
- `contentShape` can enlarge or redirect the tappable area beyond what is visually obvious.

Prove the owner by temporarily:

- coloring the suspect layer
- adding a border to the hit region
- disabling hit testing on the suspect layer

If the underlying control starts working, the invisible layer was the interaction owner.

## GeometryReader, Background, Overlay, PreferenceKey

- A top-level `GeometryReader` participates in layout and often changes the very thing you are trying to measure.
- A `GeometryReader` inside `overlay` or `background` is usually safer when you only want observation.
- `PreferenceKey` and anchor-preference flows can create feedback loops if the measured value is written back into the same subtree's layout.
- `overlay` and `background` attached at the wrong level often make the wrong view look broken.

Questions to answer:

- Is the measuring view changing layout instead of only observing it?
- Is the measurement feeding back into the same subtree and causing churn?
- Is the overlay or background attached to the real owner?

## Lazy Containers

Use lazy variants when:

- the collection is large
- creation cost is real
- scrolling performance or memory matters

Avoid or simplify away lazy variants during diagnosis when:

- the collection is small and static
- you need stable lifetime evidence for every child
- a transition or geometry handoff assumes offscreen children still exist
- reuse or deferred creation is obscuring the bug

`List` and `Table` add their own reuse, selection, and platform behavior. Do not assume they behave like a plain `VStack`.
