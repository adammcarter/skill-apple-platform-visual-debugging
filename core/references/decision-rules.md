# Decision Rules

Choose the smallest debugging surface that can answer the next visual question.

## Start Here

- First read the relevant UI code and decide whether the bug is already obvious.
- Keep edits diagnostic only. In this skill, do not refactor or implement the production fix.
- If the issue remains ambiguous, add targeted console instrumentation before asking for artifacts or manual debugger work.
- If the unknown is size, bounds, clipping, overlap, safe-area ownership, z-order, or hit region, use visual debugging.
- If the unknown is layout ownership, masking, transforms, or render ordering, use layout and rendering.
- If the unknown is SwiftUI identity churn, subtree replacement, or visible ownership, use the SwiftUI investigation reference.
- If the unknown is a transition seam, flicker, or temporal ownership problem, use animation and transitions.
- If the problem spans host views, layers, constraints, or SwiftUI interop boundaries, use the UIKit/AppKit/layers reference.
- If the unknown is hitching, jank, repeated work, or hot-path cost, use performance and Instruments.
- If the bug is hard to trigger, capture, or prove fixed, use repro and verification plus screenshot guidance.

## Escalation Order

1. Read the code and identify the likely visual owner.
2. If the likely root cause is already clear, state it and decide whether one confirming probe is still needed.
3. Add the cheapest probe that can answer one unknown.
4. Observe the result.
5. Narrow the scope.
6. Ask for screenshots only when the code and probes are no longer enough.
7. Ask the user to use the Xcode debugger, view debugger, or Instruments only when manual inspection is cheaper than another code probe and the exact requested output is clear.
8. Once the root cause is evidenced, stop debugging and hand off to the best implementation-oriented skill for the actual fix.

## When To Switch Probe Type

- If code reading already reveals the owner and likely cause, stop adding probes and prepare the handoff instead of instrumenting further.
- If one logging pass still leaves the visible owner ambiguous, switch to visual probes rather than adding more logs.
- If visual probes clarify ownership but the user cannot communicate the failure well in text, request screenshots.
- If screenshots are still ambiguous, tighten the probe or the crop before asking for more artifacts.
- If two passes of the same probe type did not increase clarity, switch probe type.

## Probe Selection Heuristics

- Prefer logs over screenshots when the next unknown is still about state, branch choice, or visible ownership.
- Prefer visual probes over logs for layout, clipping, overlap, hit testing, and animation seams.
- Prefer evidence gathered automatically by the agent over asking the user to inspect the debugger manually.
- Prefer screenshots over video whenever still frames can show the issue.
- Prefer cropped app-window captures over broad desktop or simulator captures.
- Prefer signposts and Instruments over console timestamps when the issue is performance.
- When Instruments is necessary, prefer one named template and one short repro loop over a vague "profile it" request.
- Prefer reproducing the bug in a controlled default state over manually navigating to it repeatedly.
- Prefer handing off to an implementation skill once the answer is clear rather than mixing diagnosis and permanent fixes in one pass.

## What Not To Do

- Do not jump to a refactor when the failure mode is still ambiguous.
- Do not layer several unrelated probes at once unless the bug is inherently multi-surface.
- Do not flood the console on a hot path unless log volume is itself the point of the experiment.
- Do not keep temporary debug visuals subtle. If it is a debug probe, make it unmistakable.
- Do not assume the first view that looks wrong is the true owner.
- Do not ask for video before trying the minimum useful screenshot set.
- Do not send the user spelunking through the Xcode view debugger unless you can name the exact hierarchy fact or screenshot you need from it.
- Do not send the user into Instruments without naming the exact template, the exact repro step, and the exact result you need back.
- Do not mix permanent product changes or refactors into a debugging pass. Keep code changes additive and easy to remove.
- Do not keep adding probes after the likely root cause is already well supported. Package the evidence and hand off.
