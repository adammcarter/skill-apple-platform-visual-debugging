# Example Implementations

Use these examples when the user explicitly wants concrete instrumentation patterns.

## Reusable SwiftUI Debug Modifier

```swift
import SwiftUI

private struct DebugBounds: ViewModifier {
    let name: String
    let color: Color

    func body(content: Content) -> some View {
        content
            .background(color.opacity(0.10))
            .overlay {
                Rectangle()
                    .stroke(color, lineWidth: 2)
            }
            .overlay(alignment: .topLeading) {
                Text(name)
                    .font(.caption2.monospaced())
                    .padding(4)
                    .background(.black.opacity(0.8))
                    .foregroundStyle(.white)
            }
    }
}

extension View {
    func debugBounds(_ name: String, color: Color = .red) -> some View {
        modifier(DebugBounds(name: name, color: color))
    }
}
```

Use this when several sibling views need the same visible treatment.

## Reusable Geometry Readout Modifier

```swift
import SwiftUI

private struct DebugGeometryOverlay: ViewModifier {
    let name: String
    let color: Color

    func body(content: Content) -> some View {
        content.overlay {
            GeometryReader { proxy in
                ZStack(alignment: .topLeading) {
                    Rectangle()
                        .stroke(color, lineWidth: 1)

                    Text("\(name) \(Int(proxy.size.width))x\(Int(proxy.size.height))")
                        .font(.caption2.monospaced())
                        .padding(4)
                        .background(.black.opacity(0.8))
                        .foregroundStyle(.white)
                        .offset(x: 4, y: 4)
                }
            }
        }
    }
}

extension View {
    func debugGeometry(_ name: String, color: Color = .cyan) -> some View {
        modifier(DebugGeometryOverlay(name: name, color: color))
    }
}
```

Use this when the next question is about actual rendered size or bounds ownership and you want a reusable overlay that does not participate in layout.

## Reusable Hit-Test Probe Modifier

```swift
import SwiftUI

private struct DebugHitTestProbe: ViewModifier {
    let name: String
    let color: Color
    let disableHitTesting: Bool

    func body(content: Content) -> some View {
        content
            .background(color.opacity(0.12))
            .overlay {
                Rectangle()
                    .stroke(color, style: StrokeStyle(lineWidth: 2, dash: [6, 4]))
            }
            .overlay(alignment: .topLeading) {
                Text("\(name) hitTest=\(disableHitTesting ? "off" : "on")")
                    .font(.caption2.monospaced())
                    .padding(4)
                    .background(.black.opacity(0.8))
                    .foregroundStyle(.white)
            }
            .allowsHitTesting(!disableHitTesting)
    }
}

extension View {
    func debugHitTestProbe(
        _ name: String,
        color: Color = .blue,
        disableHitTesting: Bool = false
    ) -> some View {
        modifier(
            DebugHitTestProbe(
                name: name,
                color: color,
                disableHitTesting: disableHitTesting
            )
        )
    }
}
```

Use this when you need a reusable probe for invisible overlays or oversized tap regions. Keep `disableHitTesting` off until the hypothesis is specific enough to justify changing interaction temporarily.

## Reusable Scoped Debug Logger

```swift
import Foundation

private struct VisualDebugLogger {
    let prefix: String
    let id: String

    init(prefix: String, id: String? = nil) {
        self.prefix = prefix
        self.id = id ?? UUID().uuidString
    }

    func log(_ event: String, fields: [String: String] = [:]) {
        let details = fields
            .sorted { $0.key < $1.key }
            .map { "\($0.key)=\($0.value)" }
            .joined(separator: " ")

        if details.isEmpty {
            print("[\(prefix)] id=\(id) \(event)")
        } else {
            print("[\(prefix)] id=\(id) \(event) \(details)")
        }
    }
}
```

```swift
private let logger = VisualDebugLogger(prefix: "ComparisonStage", id: item.id.uuidString)

.onAppear {
    logger.log("appear")
}
.onChange(of: phase) { oldValue, newValue in
    logger.log(
        "phase-change",
        fields: [
            "old": "\(oldValue)",
            "new": "\(newValue)"
        ]
    )
}
```

Use this when the same stable prefix and ID need to show up across several temporary probes and you want to avoid hand-building log strings repeatedly.

## Minimal Logging Pair

```swift
.onAppear {
    print("[SliderChrome] appear id=\(id)")
}
.onChange(of: isClipped) { _, newValue in
    print("[SliderChrome] clipped=\(newValue)")
}
```

Use this when the question is whether a visible state changed at the wrong time. Do not add unrelated geometry or model dumps unless they change the next debugging decision.

## Temporary Debug ID

```swift
@State private var debugID = UUID().uuidString

.onAppear {
    print("[InspectorRow] appear debugID=\(debugID)")
}
.onDisappear {
    print("[InspectorRow] disappear debugID=\(debugID)")
}
```

Use this when the code has no stable entity ID but you still need to distinguish repeated appearances of the same visual owner during one debugging pass.

## Bad vs Good Logging Set

Bad:

```swift
print(viewModel)
print(proxy.size)
print(isDragging)
print(phase)
print(offset)
print(selection)
```

Good:

```swift
.onChange(of: phase) { oldValue, newValue in
    print("[ComparisonStage] phase \(oldValue) -> \(newValue)")
}
.onChange(of: isClipped) { _, newValue in
    print("[ComparisonStage] clipped=\(newValue)")
}
```

The bad version produces activity. The good version answers one visual question: did the visible phase change or clipping state change at the wrong time?

## Transition Trace

```swift
.onAppear {
    print("[TransitionHost] appear phase=\(phase) id=\(id)")
}
.onDisappear {
    print("[TransitionHost] disappear phase=\(phase) id=\(id)")
}
.onChange(of: phase) { oldValue, newValue in
    print("[TransitionHost] phase \(oldValue) -> \(newValue)")
}
```

Use this when the bug may be tied to subtree replacement or transition timing.

## Identity Churn Trace

```swift
let _ = Self._printChanges()

.onAppear {
    print("[CardRow] appear id=\(item.id)")
}
.onDisappear {
    print("[CardRow] disappear id=\(item.id)")
}
```

Use this when you suspect `if`, `switch`, `ForEach`, or `.id(...)` is recreating a subtree and resetting visible state.

## Invisible Hit-Test Owner Probe

```swift
suspectOverlay
    .background(Color.blue.opacity(0.12))
    .overlay {
        Rectangle()
            .stroke(.blue, lineWidth: 2)
    }
    .allowsHitTesting(false)
```

Use this temporarily when a visually empty layer may still be stealing taps. If the underlying control starts working, the suspect layer was the interaction owner.

## Bad vs Good Geometry Observation

Bad:

```swift
GeometryReader { proxy in
    content
        .frame(width: proxy.size.width)
}
```

Good:

```swift
content
    .overlay {
        GeometryReader { proxy in
            Text("\\(Int(proxy.size.width))")
                .font(.caption2.monospaced())
                .padding(4)
                .background(.black.opacity(0.75))
                .foregroundStyle(.white)
        }
    }
```

The bad version changes layout while measuring it. The good version observes layout with much less risk of becoming the bug.

## UIKit Visual Probe

```swift
view.backgroundColor = UIColor.systemBlue.withAlphaComponent(0.12)
view.layer.borderWidth = 2
view.layer.borderColor = UIColor.systemBlue.cgColor
```

Use this when debugging host views, wrappers, or layer ownership.

## Representable Sizing Probe

```swift
final class DebugWrappedView: UIView {
    override func layoutSubviews() {
        super.layoutSubviews()
        print("[WrappedView] bounds=\(bounds)")
        layer.borderWidth = 2
        layer.borderColor = UIColor.systemPink.cgColor
    }
}
```

Use this when a `UIViewRepresentable` draws incorrectly or clips unexpectedly and you need to know what size the wrapped platform view actually receives.

## Signpost Helper For Instruments

```swift
import OSLog

private struct VisualDebugSignposter {
    private let signposter: OSSignposter
    private let name: StaticString

    init(
        subsystem: String = Bundle.main.bundleIdentifier ?? "VisualDebug",
        category: String,
        name: StaticString
    ) {
        signposter = OSSignposter(subsystem: subsystem, category: category)
        self.name = name
    }

    func withInterval<T>(_ body: () throws -> T) rethrows -> T {
        let signpostID = signposter.makeSignpostID()
        let state = signposter.beginInterval(name, id: signpostID)
        defer { signposter.endInterval(name, state) }
        return try body()
    }
}
```

```swift
private let transitionSignposter = VisualDebugSignposter(
    category: "ComparisonStage",
    name: "TransitionPreparation"
)

let preparedState = transitionSignposter.withInterval {
    prepareTransitionState()
}
```

Use this when a hitch or stutter is visible and you need one clearly named interval in Instruments without adding broad timing logs to a hot path.

## Screenshot Request Example

Good:

```text
Please capture one screenshot of the broken state, cropped to the app window only.
If the clipping seam only appears during the transition, capture before, after, and one intermediate frame showing the seam.
```

Bad:

```text
Please send a screen recording or a few screenshots of the app.
```

The good request limits cost and makes the next decision obvious.

## Handoff Example

Good:

```text
Likely root cause: the parent container owns clipping through `clipShape`, not the child that looks broken.
Owner: `ComparisonStage` in `ComparisonStageView.swift`.
Evidence: parent/child color probes showed the seam lives at the parent boundary, and phase logs showed no unexpected state churn.
Fix direction: move clipping to the transitioning content layer or split the outgoing and incoming layers so the parent no longer clips both states.
Next skill: if installed, use `apple-platform-frontend` to implement and verify the UI fix.
```

Use this when the root cause is clear and the next step should move to an implementation-oriented skill without repeating the investigation.
