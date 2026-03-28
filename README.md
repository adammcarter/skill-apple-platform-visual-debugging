# Apple Platform Visual Debugging 🔎

Debug Apple UI bugs with the cheapest next probe first.

This skill helps agents diagnose layout, rendering, hit-testing, layering, animation, and transition bugs using additive, easy-to-remove probes instead of speculative refactors. Once the evidence is clear, it can hand the fix over to the right implementation skill.

## What it can do

- Read the code, form a likely hypothesis, and choose the cheapest next debugging step instead of guessing.
- Add minimal logs, overlays, debug IDs, signposts, screenshots, or debugger steps that are easy to remove later.
- Tell you when the next move should be code probes, the Xcode view debugger, Instruments, or a handoff to an implementation skill.

## Solves problems like

- A view is clipped, overlapped, untappable, flickering, misplaced, or animating incorrectly.
- It is unclear whether the next step should be logs, visual probes, the Xcode view debugger, or Instruments.
- A bug needs better evidence before anyone starts changing product code.

## Good when you want to

- Use it when something looks wrong on screen and you are not sure where to start.
- Use it to ask for the cheapest next debugging step before changing product code.
- Use it to isolate interop, transition, hit-testing, or rendering issues and gather clean evidence before handing the fix off.

## Example prompts

- `Use $apple-platform-visual-debugging to help me debug why this SwiftUI button looks visible but is not tappable.`
- `Use $apple-platform-visual-debugging to tell me the cheapest next debugging step for this flickering transition.`
- `Use $apple-platform-visual-debugging to add minimal logging and probes so we can prove whether this is a layout bug or a hit-testing bug.`
- `Use $apple-platform-visual-debugging to guide me through the Xcode view debugger for this overlapping host-view issue.`
- `Use $apple-platform-visual-debugging to instrument this animation with the smallest useful logs and signposts before we try a fix.`

## Skill Judge

This skill scored `120/120` using the separate `skill-judge` skill.

<https://github.com/softaworks/agent-toolkit/blob/main/skills/skill-judge/SKILL.md>

## Install

With skills.sh:

```bash
npx skills add adammcarter/skill-apple-platform-visual-debugging
```

For a local Codex install:

```bash
ln -s ~/repos/skill-apple-platform-visual-debugging ~/.codex/skills/apple-platform-visual-debugging
```
