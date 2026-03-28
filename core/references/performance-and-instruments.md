# Performance And Instruments

Use this reference when the visual issue is jank, stutter, slow scrolling, expensive layout, heavy invalidation, or unexplained repeated work.

## Core Bias

- Do not debug performance from intuition alone.
- Measure first, then narrow, then fix.
- Prefer Instruments, signposts, and targeted counters over guessing from logs.
- Keep this in the visual-debugging lane: focus on rendering, layout, animation, and visible UI cost.
- Treat Instruments as a later, higher-cost move for the user. Prefer automatic evidence from code probes and signposts first when that can answer the question.
- If the user must run Instruments, assume they are new to it unless they say otherwise.

## Good Questions

- Which action causes the hitch?
- Is the cost CPU, layout, rendering, I/O, or repeated invalidation?
- Is expensive work happening once, or on every frame or state change?

## First Pass

1. Reduce the repro to one user action if possible.
2. Add signposts or stable timing logs around the suspected boundary.
3. Use Instruments to identify where time is really going.
4. Correlate the trace with visible UI behavior.

## Useful Instruments

- Time Profiler for CPU-heavy work and repeated hot paths
- Core Animation for dropped frames and rendering smoothness
- Animation Hitches when the visible issue is a hitch during scrolling or transition playback and that instrument is available
- SwiftUI when body invalidation, update churn, or repeated view work is the suspected source
- Points of Interest / Signposts for correlating app phases to timeline events
- Allocations when the issue looks like repeated construction or churn
- Leaks when the app degrades across repeated repro loops

## How To Guide The User

When you send the user into Instruments, do not assume prior experience. Tell them exactly:

1. which template to open
2. whether to launch from Xcode or attach to a running app
3. when to press Record
4. the single repro action to perform
5. when to stop recording
6. what exact screenshot, metric, or top stack to send back

Good example:

- Open Instruments and choose `Core Animation`.
- Launch the app from that template.
- Press Record, perform one slow scroll on the affected screen, then stop recording immediately.
- Send back one screenshot of the track showing the hitch or frame drop region.

Better example for CPU cost:

- Open Instruments and choose `Time Profiler`.
- Start recording before tapping the button that triggers the hitch.
- Perform that tap once, wait for the hitch to finish, then stop.
- Send back the top call tree entries during the spike, or one screenshot of the hottest stack.

## When To Choose Which Template

- `Time Profiler`: the app feels slow because work is burning CPU
- `Core Animation`: frames drop, scrolling stutters, or rendering feels uneven
- `Animation Hitches`: the user sees discrete hitches during motion and you want hitch events called out directly
- `SwiftUI`: the UI seems to redraw or recompute excessively and SwiftUI invalidation is the likely suspect
- `Points of Interest / Signposts`: you already have signposts or can add them and need to correlate timeline events to one visible hitch
- `Allocations`: repeated creation, churn, or memory growth seems tied to the visible slowdown
- `Leaks`: repeated repro loops degrade the app over time

## Signpost Candidates

- image decode and resize
- diff or comparison processing
- transition preparation
- repeated layout passes
- expensive model recomputation
- cache misses and reload loops

## Warning Signs

- Debug logging in a hot animation path used as a performance probe.
- Visual effects added to a dense, rapidly changing surface without measurement.
- Broad state invalidation used to "keep things in sync."
- Geometry probes left active while diagnosing frame-by-frame issues.
- Treating Time Profiler output as a fix list without relating it back to the user-visible hitch.
- Telling the user to "open Instruments and see what happens" without naming a template and a single repro action.
