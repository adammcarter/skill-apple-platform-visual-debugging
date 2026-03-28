# Animation And Transitions

Use this reference when the bug is a seam, flicker, jump, ownership swap, mismatched geometry, or phase timing issue during motion.

## Core Bias

- Transition bugs are temporal. Static screenshots are supportive evidence, not the whole story.
- First prove which layers coexist during the transition and which one owns clipping, masking, or z-order.
- Prefer one controlled transition loop over manually reproducing the issue from scratch each time.

## First Probe Set

1. Add distinct debug colors to outgoing and incoming layers.
2. Label both layers on screen.
3. Log the phase or state transition.
4. Temporarily disable animation locally to separate geometry bugs from timing bugs.
5. Ask for before and after screenshots, then only the minimum useful intermediate frames if needed.

## Questions To Answer

- Do both old and new views coexist briefly?
- Is the outgoing layer still clipping the incoming one?
- Is the view being recreated instead of transitioned?
- Is the state flip happening before geometry or content is ready?
- Is the artifact caused by layout, animation timing, or identity churn?

## Good Temporary Tactics

- color the old and new layers differently
- log phase transitions with stable IDs
- add timing only when ordering is the question, and prefer a timestamp or sequence number over broad state dumps
- log the animated property under suspicion: geometry for position or size bugs, opacity for fade bugs, or progress only when progress itself is the unknown
- slow the animation only if that reveals ownership more clearly
- disable the animation locally to see whether the steady-state layout is already wrong
- request still frames before escalating to video

## Red Flags

- declaring victory from one good-looking frame
- adding more animation modifiers before proving the ownership problem
- asking for video before checking whether endpoint and intermediate screenshots already show the seam
