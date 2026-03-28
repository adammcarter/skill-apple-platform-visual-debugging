# Repro And Verification

Use this reference when the hardest part is making the bug appear reliably, capturing evidence, or proving the fix.

## Build A Repro Loop

- Reduce the bug to the smallest repeatable interaction.
- Prefer code-driven default state over brittle UI scripting when you need a precise UI configuration.
- If the bug is temporal, start with before and after screenshots plus selected intermediate frames before escalating to video.
- If the bug is intermittent, add lightweight counters or timestamps so repeated attempts can be compared.

## Evidence Quality

Strong evidence:

- a short deterministic repro path
- before and after screenshots for layout bugs
- before and after screenshots plus selected intermediate frames for transition bugs
- logs with stable prefixes and IDs
- a breakpoint or call stack at the exact mutation site

Weak evidence:

- one anecdotal run
- logs without identity or timing
- screenshots taken before animation settles
- broad uncropped captures with lots of unrelated chrome
- "seems fixed" without replaying the repro

## Verification Questions

- Can another engineer reproduce the same failure from the notes?
- Does the evidence show cause, or only correlation?
- Did the fix remove the failure mode, or only make it harder to notice?

## Cleanup

- Remove or gate temporary probes after verification.
- Keep only reusable helpers or tests that continue to earn their cost.
