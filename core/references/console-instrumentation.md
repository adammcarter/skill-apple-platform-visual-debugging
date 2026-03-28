# Console Instrumentation

Use this when the UI bug is still ambiguous after reading the code and the question is "what happened, in what order, and with what visible owner or state?"

## Core Bias

- Good debug logs explain one causal story.
- Bad debug logs merely prove the app is busy.
- Prefer boundary logs and state transitions over line-by-line narration.
- For UI debugging, log only what helps explain the visible symptom.

## Preferred Pattern

1. Trace only around the suspect code path.
2. Include a stable prefix.
3. Include enough identity to distinguish repeated events.
4. Include old and new values for state transitions.
5. Include timing only when sequencing matters for the visible bug.

## Minimum Useful Log Shape

Every log should answer one specific visual question.

- Include a stable prefix.
- Include a stable entity or view ID. If the code has no stable ID, add a UUID-backed temporary debug ID as close to the likely owner as possible.
- Include only the fields needed for the active hypothesis.
- Include a timestamp, sequence number, or elapsed time only for temporal or intermittent bugs where ordering matters.

Examples:

- frame or geometry bug: log `x`, `y`, `width`, and `height`, or the specific derived size or offset under suspicion
- opacity or visibility bug: log `opacity`, hidden state, or branch choice, not unrelated geometry
- transition bug: log phase, stable ID, and the one animated value most likely to explain the seam
- hit-testing bug: log the suspected owner, hit-testing state, and the one visibility or shape value that could explain the wrong target
- collection lifetime bug: log item ID plus appear, disappear, or branch-change events

## Swift Logging

```swift
print("[ComparisonStage] phase \(oldValue) -> \(newValue)")
debugPrint("[ComparisonStage] model:", model)
dump(viewModel)
```

Use:

- `print` for simple readable traces
- `debugPrint` when debug representation helps
- `dump` for one-off structural inspection, not hot paths

## Unified Logging

```swift
import OSLog

private let logger = Logger(subsystem: "com.example.app", category: "Comparison")

logger.debug("drag phase changed to \(String(describing: phase))")
logger.error("image load failed: \(error.localizedDescription)")
```

Prefer `Logger` when the project already uses it or when log levels matter.

## What To Include

- entity or view ID
- old and new phase
- branch choice or visibility state when relevant
- count, size, or index when collections matter
- one user-action boundary when that makes the log interpretable
- the specific visual property under suspicion rather than a broad state dump

## What To Avoid

- giant payload dumps by default
- repeating the same log in every helper on the same path
- success-path spam with no decision value
- raw private data unless explicitly justified and safe
- values that do not change the next debugging decision
- timestamps on non-temporal logs
- mixing geometry, opacity, visibility, and unrelated model state into one line when only one of them is needed

## SwiftUI Probe Points

Useful hooks:

- `onAppear`
- `onDisappear`
- `onChange(of:)`
- `.task`
- gesture handlers
- async completion points

```swift
.onAppear {
    print("[SliderChrome] appear id=\(id)")
}
.onDisappear {
    print("[SliderChrome] disappear id=\(id)")
}
.onChange(of: phase) { oldValue, newValue in
    print("[SliderChrome] phase \(oldValue) -> \(newValue)")
}
```

For invalidation debugging on a suspicious subtree:

```swift
let _ = Self._printChanges()
```

Use it surgically.

## Geometry and Derived Value Probes

```swift
let resolvedWidth = proxy.size.width - inset * 2
print("[Inspector] width=\(proxy.size.width) inset=\(inset) resolved=\(resolvedWidth)")
```

Log derived values close to where they are computed.

## Noisy Paths

When logs would otherwise become spammy, gate them:

```swift
private let isDebugLoggingEnabled = true

if isDebugLoggingEnabled {
    print("[Transition] progress=\(progress)")
}
```

Keep the flag local to the probe so cleanup is obvious.

## Better Than Spam

When you only need to stop on one suspicious event, prefer:

- a conditional breakpoint
- a one-shot debugger command
- a signpost around the hot path
- a temporary counter plus threshold log

## Example: Minimal UI State Trace

```swift
.onChange(of: phase) { oldValue, newValue in
    print("[ComparisonStage] phase \(oldValue) -> \(newValue)")
}
.onChange(of: isClipped) { _, newValue in
    print("[ComparisonStage] clipped=\(newValue)")
}
```

This is better than logging every geometry value on every update when the real question is whether the visible phase changed at the wrong time.

If the question were about opacity instead, log opacity. If the question were about geometry instead, log the relevant size or position values. Do not combine all of them unless the hypothesis truly depends on all of them.
