# Layout And Rendering

Use this reference when the bug is about frames, clipping, overlap, safe areas, masks, corner radius ownership, transforms, or render ordering.

## First Principles

- The visually wrong view is often not the owner of the bug.
- Inspect the nearest parent that owns layout, clipping, safe-area application, masking, or compositing.
- For animation seams, inspect both steady-state layout and the transient overlap between old and new states.

## Probe Order

1. Color the parent and child differently.
2. Add visible borders to both.
3. Label the suspicious subtree.
4. Add geometry readout if the size or position is surprising.
5. Inspect z-order and hit testing.
6. If still ambiguous, use Xcode's view debugger as a targeted manual fallback with one specific hierarchy question to answer.

## Common Ownership Mistakes

- The child looks clipped, but the parent mask or corner radius is responsible.
- A safe-area inset is applied at one level while the background is drawn at another.
- A transition appears broken because both views exist briefly and the outgoing view still owns clipping.
- The wrong view appears offset because an ancestor applies padding, transform, alignment, or frame constraints unexpectedly.

## Pixel Seams And Fractional Layout

- One-pixel seams often come from fractional widths, offsets, or stroke placement rather than the wrong owner.
- A stroke centered on an edge can land on half-pixels and look blurry or doubled.
- Screenshots or snapshots taken at a different scale can make a seam look worse or disappear.

Questions to answer:

- Is the size or offset fractional when it should be integral?
- Is the stroke centered when an inset or outside stroke is needed?
- Is the seam a real runtime issue, or an artifact of capture scale?

## Compositing, Materials, And Blur

- `.drawingGroup()` and `.compositingGroup()` can change clipping, blending, and antialiasing behavior.
- Materials, blur, and vibrancy can make bounds look wrong even when layout is correct.
- Masks and blend operations often move the real rendering owner higher in the tree than expected.

Ask:

- Did a compositing modifier change the layer that actually draws the result?
- Is the blur or material making a spacing problem look like a clipping problem?
- Is a mask, material, or blend mode attached at the right level?

## SwiftUI Questions

- Is a `.frame(maxWidth: .infinity)` or alignment wrapper changing layout ownership?
- Is `overlay`, `background`, `mask`, or `clipShape` attached at the right level?
- Is a `GeometryReader` participating in layout when it should only observe it?
- Is a branch swap replacing the subtree during transition?

## UIKit And AppKit Questions

- Which view owns `clipsToBounds`, corner radius, transform, or content insets?
- Is Auto Layout solving differently than the visual intuition suggests?
- Is the backing layer tree the real source of the artifact?

## Red Flags

- Adding padding or offsets as a "visual fix" before understanding the owner.
- Chasing the leaf view while the parent owns clipping.
- Taking one static screenshot as conclusive for an animation bug.
- Ignoring fractional geometry when the visible bug is a hairline seam.
