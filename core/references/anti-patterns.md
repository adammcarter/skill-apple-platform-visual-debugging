# Anti-Patterns

Avoid these during debugging:

- speculative fixes before the failure mode is visible
  The result may look better while the real owner stays unknown.
- broad refactors used as debugging substitutes
  They change too many variables and destroy the evidence trail.
- logging every layer of the same state flow
  This creates output volume, not clarity.
- subtle debug colors or tiny labels
  If the probe is hard to read in a screenshot, it failed its job.
- leaving temporary probes mixed into production logic
  The next person will not know what is signal and what is leftover scaffolding.
- drawing conclusions from one screenshot when the bug is temporal
  A single frame can hide ownership swaps and clipping handoff problems.
- assuming the visually broken view is also the owner of the bug
  Parent containers, host views, and layers frequently own the real behavior.
- using debugger-only tactics for a bug that needs persistent visual proof
  A paused stack trace does not explain what the user actually sees over time.
- adding more animation or layout modifiers before proving the owner
  This often masks the bug instead of locating it.
- asking for full-screen or video artifacts before trying small cropped screenshots
  Irrelevant pixels dilute attention and make the next decision slower.

When the same probe fails twice to clarify the issue, change probe type instead of repeating the same experiment.
