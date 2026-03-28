# Review Rubric

Use this reference when reviewing Apple-platform debugging changes or a debugging plan.

## Review Output Shape

- Findings first.
- Lead with wrong probe choice, missing causal evidence, or debug code likely to leak into shipped paths.
- If no issues stand out, say which axes were checked so the review still feels deliberate.

## Immediate Failure Signals

- The debugging change adds lots of output but does not answer a specific question.
- Logs have no stable prefixes, IDs, or state transition detail.
- Logs include broad unrelated fields instead of the minimum data needed for the active hypothesis.
- The debugging change mixes in product logic changes or refactors instead of staying additive and removable.
- Visual probes are too subtle to read in screenshots or video.
- Screenshot requests are broader than needed or include lots of unrelated UI.
- Manual tool requests assume Xcode or Instruments experience without giving exact steps.
- The code instruments the leaf view while the likely owner is a parent, host view, or layer.
- A suspected identity or ownership bug is being "fixed" before visual evidence exists.
- Breakpoints or debugger guidance are proposed where persistent visual or screenshot evidence would be more useful.

## Order Of Evaluation

1. Probe choice
2. Evidence quality
3. Scope and cleanup
4. Root-cause coverage
5. Verification plan
6. Artifact cost discipline
7. Handoff quality

## Probe Choice

- Did the author pick the right debugging surface: console, visual, debugger, or Instruments?
- Is the instrumentation the smallest change that can answer the next unknown?
- Does the plan clearly state what result would confirm or reject the hypothesis?

## Evidence Quality

- Are logs searchable and identity-rich?
- Do the logs use a stable ID or a UUID-backed temporary debug ID when no stable ID exists?
- Do the logs capture only the property or fields that matter to the current hypothesis?
- Are visual probes obvious and localized?
- Is the bug temporal, and if so, is the screenshot/frame request sufficient before escalating to video?
- Is the debugger plan precise about where and why execution should stop?
- If the user is asked to use Instruments or Xcode tools manually, are the steps concrete enough for a novice to follow?

## Scope And Cleanup

- Are the edits limited to additive debug-only code rather than permanent behavior changes?
- Is debug-only code clearly temporary or gated?
- Are reusable helpers extracted only when repetition is real?
- Is there a cleanup path after the hypothesis is validated?

## Handoff Quality

- Does the debugging output leave a clear root-cause summary for the next skill?
- Does it identify the likely owner, relevant files or symbols, and the smallest reasonable fix direction?
- Does it recommend an appropriate implementation-oriented skill instead of drifting into refactor work inside the debugging pass?

## Root-Cause Coverage

- Does the probe target the actual owner of the behavior?
- Are parent-child ownership or layer boundaries being ignored?
- Does the proposal distinguish symptom from cause?

## Verification Plan

- Is there a deterministic repro path?
- Does the plan say what to run and what signal to watch for?
- Would another engineer be able to reproduce and validate the same result?

## Artifact Cost Discipline

- Is the requested artifact set the minimum useful set?
- Could cropped screenshots answer the question before video or full-screen capture?
- Is the request specific enough that the next artifact will actually change the debugging decision?
