---
name: apple-platform-visual-debugging
description: Use this skill when the task is diagnosing, instrumenting, isolating, or verifying visual UI bugs on Apple platforms, especially SwiftUI, UIKit, AppKit, host views, layers, layout, rendering, clipping, overlap, hit testing, z-order, safe areas, animations, and transitions. It specializes in choosing the cheapest next probe and using additive, easy-to-remove logs, targeted visual markers, debugger tools, and minimal cropped screenshots for issues like misplaced, clipped, flickering, overlapping, untappable, or incorrectly animated views.
---

# Apple Platform Visual Debugging

This is the canonical Apple visual debugging skill package for Codex/OpenAI-style agents.

Always load:

- `./core/prompt.md`

Then load only the references that match the task:

Start with the smallest route that answers the next unknown. Do not load multiple primary probe references on the first pass unless the bug clearly spans multiple surfaces.

If you do not yet know the cheapest next debugging move or need to narrow an investigation:

- `./core/references/decision-rules.md`

If the issue is ambiguous after reading the code and needs precise, low-noise instrumentation:

- `./core/references/console-instrumentation.md`

If the issue is about frames, bounds, clipping, overlap, safe areas, hit testing, or z-order:

- `./core/references/visual-debugging.md`
- `./core/references/layout-and-rendering.md`

If the issue is specifically about SwiftUI identity, subtree replacement, invalidation, common state-driven visual bugs, invisible hit regions, or layout-observing views:

- `./core/references/swiftui-investigation.md`
- `./core/references/swiftui-common-failures.md`

If the issue is specifically about animation seams, flicker, timing, or ownership during transitions:

- `./core/references/animation-and-transitions.md`

If the issue spans UIKit, AppKit, host views, layers, or interop boundaries:

- `./core/references/uikit-appkit-and-layers.md`

If the issue needs Xcode-native visual debugging workflows such as the view debugger, targeted breakpoints, or LLDB inspection:

- `./core/references/xcode-debugger-tools.md`

If the issue is about visual hitching, stutter, or expensive rendering work:

- `./core/references/performance-and-instruments.md`

If the work needs reproducibility, screenshot guidance, cropping rules, or a validation plan:

- `./core/references/repro-and-verification.md`
- `./core/references/screenshot-artifacts.md`

If you are reviewing or untangling bad debugging practice:

- `./core/references/anti-patterns.md`
- `./core/references/review-rubric.md`

If the user explicitly asks for examples:

- `./core/references/example-prompts.md`
- `./core/references/example-implementations.md`

If you are blocked on a tricky Apple UI or Xcode behavior and need authoritative follow-up sources:

- `./core/references/trusted-resources.md`

Do not auto-load review docs, example docs, performance docs, or trusted resources unless the task clearly needs them.
Do not load `screenshot-artifacts.md` until code reading and instrumentation have stopped being enough.
Do not load `example-implementations.md` just to answer a diagnosis question; load it only when concrete probe code is needed.
Do not load `xcode-debugger-tools.md` when targeted logs or visual probes can answer the question more cheaply.
Do not load `trusted-resources.md` on the first pass.

Additional OpenAI/Codex-specific behavior:

- Read the code first and identify likely root cause before adding probes.
- If the bug remains ambiguous, prefer the smallest instrumentation patch that yields decisive evidence.
- Do not implement product fixes or refactors in this mode. Limit edits to additive debug-only probes, logs, overlays, labels, signposts, or temporary gates that are easy to remove.
- Once the root cause is evidenced, recommend the best implementation-oriented skill for the follow-up fix and pass along the relevant evidence, files, and likely fix direction.
- If installed, prefer `apple-platform-frontend` for UI-layer fixes and `swift-development` for deeper state, architecture, or non-UI logic fixes. Otherwise recommend the best available implementation-oriented skill.
- Prefer targeted logs before asking for screenshots unless the problem is obviously visual and logs will not answer the next unknown.
- When screenshots are needed, ask for the minimum useful set and prefer cropped app-window captures over broader desktop context.
- Keep close-out summaries brief and verification-oriented.
- When reviewing debugging changes, present findings first and prioritize signal quality, noisy probes, and weak causal evidence.
