# Screenshot Artifacts

Use this reference when the next useful debugging evidence should come from images rather than more code instrumentation.

## Core Bias

- Ask for screenshots only when code reading and low-noise logs are no longer enough.
- Ask for the minimum useful screenshot set.
- Crop aggressively so the model only sees the relevant app content.
- Prefer still frames over video whenever still frames can answer the question.

## Default Rules

- Prefer the app window only.
- Crop out desktop chrome, simulator chrome, toolbars, sidebars, inspectors, or surrounding UI that is clearly unrelated.
- If only one region matters, crop to that region instead of the whole window.
- If a bug has distinct states, ask for the smallest state set that proves the issue.

## Suggested Screenshot Sets

For layout, clipping, overlap, or safe-area bugs:

- one screenshot of the broken state
- one comparison screenshot only if a correct state exists and would clarify the issue

For transitions and animations:

- before state
- after state
- one or more intermediate frames only if the seam or ownership problem is not visible from endpoints

For hit-testing or interaction overlays:

- one screenshot with the debug probe visible
- one clean screenshot only if the contrast helps interpret the probe

## Good Request Templates

Use requests like these:

- "Please capture one screenshot of the broken state, cropped to the app window only."
- "Please capture before and after screenshots of the transition, then one intermediate frame only if the seam is not visible from the endpoints."
- "Please crop to the comparison area only; the toolbar and sidebars are not relevant."

Avoid requests like these:

- "Send a recording."
- "Send a screenshot of everything."
- "Capture a few screenshots and we’ll see."

## Do Not Ask For

- full desktop captures when the app window is enough
- long videos before trying still frames
- several near-duplicate screenshots with no clear diagnostic purpose
- chrome or surfaces that are definitely unrelated to the bug
