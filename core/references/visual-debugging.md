# Visual Debugging

Use this when the question is "where is the view really, what size is it, and what is drawing on top of what?"

## Core Bias

- Strong visual probes beat elegant ones.
- If the debug color is tasteful, it is probably too subtle.
- Instrument the parent and child together when ownership is unclear.

## SwiftUI First-Line Probes

Add temporary markers directly on the suspect view:

```swift
.background(Color.red.opacity(0.12))
.border(.red, width: 1)
```

Differentiate adjacent owners with distinct colors:

```swift
.background(Color.green.opacity(0.12))
.overlay {
    Rectangle()
        .stroke(.green, lineWidth: 2)
}
```

Recommended palette:

- parent container: red
- child content: green
- overlays or hit regions: blue
- spacers or layout shims: orange
- suspected clipping owner: pink

## Labeled Overlays

```swift
.overlay(alignment: .topLeading) {
    Text("SliderChrome")
        .font(.caption2.monospaced())
        .padding(4)
        .background(.yellow.opacity(0.9))
}
```

Use labels when multiple similar views are on screen.

## Geometry Probes

```swift
.overlay {
    GeometryReader { proxy in
        ZStack(alignment: .topLeading) {
            Rectangle().stroke(.cyan, lineWidth: 1)
            Text("\\(Int(proxy.size.width))x\\(Int(proxy.size.height))")
                .font(.caption2.monospaced())
                .padding(4)
                .background(.black.opacity(0.75))
                .foregroundStyle(.white)
        }
    }
}
```

Use this when collapsed layout, unexpected sizing, or unstable geometry is suspected.

## Parent and Child Together

Instrument both parent and child when layout ownership is unclear. Many bugs are invisible until both bounds are visible at once.

For safe-area confusion, give the root and content containers different colors so gaps become obvious.

## Z-Order and Hit Testing

- Give competing layers distinct translucent fills.
- Add a heavy border to the suspected hit region.
- Temporarily disable hit testing to prove ownership.

```swift
.allowsHitTesting(false)
```

This is a probe, not a fix.

## Invisible But Interactive

- `opacity(0)` can still leave a tappable view alive.
- `Color.clear` overlays can still intercept gestures.
- `contentShape` can make a tap target much larger than the visible content.

When the wrong thing is receiving taps, color the suspect overlay, outline the region, and temporarily disable hit testing on that layer.

## UIKit and AppKit

```swift
view.backgroundColor = UIColor.systemRed.withAlphaComponent(0.12)
view.layer.borderColor = UIColor.systemRed.cgColor
view.layer.borderWidth = 1
```

If the issue is layer-backed, instrument the actual layer that owns clipping, masks, transforms, or corner radius.

## Reusable Helpers

If visual probes are needed repeatedly, extract a clearly named debug modifier instead of scattering ad hoc overlays through the codebase.

## Anti-Patterns

- pasting several similar overlays across files without naming the probe
- debugging clipping only on the leaf view
- using tiny captions or faint colors that disappear in screenshots
- leaving geometry readers in place after the investigation ends
