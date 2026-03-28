# UIKit, AppKit, And Layers

Use this reference when the bug lives below SwiftUI or at an interop boundary.

## Core Bias

- In mixed stacks, identify whether the real owner is SwiftUI, the host view, the platform view, or the backing layer.
- Layer-backed bugs rarely yield to SwiftUI-only reasoning.

## Suspect Areas

- Auto Layout ownership
- `layoutSubviews` or `viewDidLayoutSubviews`
- `updateConstraints`
- `clipsToBounds`
- layer masks, transforms, and corner radius
- flipped coordinate systems on macOS
- view-hosting boundaries between SwiftUI and UIKit/AppKit

## Useful Probes

- temporary border and background colors on the platform view
- lifecycle logs in layout callbacks
- symbolic breakpoints for constraint and layer issues
- Xcode view debugger

## Representable Sizing

When the bug crosses `UIViewRepresentable` or `NSViewRepresentable`, ask:

- Does the wrapped view have a real intrinsic content size?
- Should the wrapper provide `sizeThatFits`, or is SwiftUI expected to infer size from constraints?
- Is the host SwiftUI container giving the representable more space than it can actually draw into?
- Is clipping happening in the SwiftUI host, the platform view, or the backing layer?

Useful proving moves:

- border the SwiftUI wrapper and the platform view in different colors
- log `bounds` during `layoutSubviews`, `layout()`, or equivalent layout callbacks
- temporarily force a known frame to distinguish sizing negotiation from drawing bugs

## Common Traps

- Fixing the hosted SwiftUI view when the host container owns sizing.
- Debugging a CALayer artifact only at the NSView or UIView level.
- Assuming constraint warnings name the only broken view instead of the broader ownership chain.
- Assuming a representable that draws correctly at a fixed frame is also negotiating size correctly with SwiftUI.
