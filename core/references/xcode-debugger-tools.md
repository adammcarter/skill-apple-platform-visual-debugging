# Xcode Debugger Tools

Use debugger-native tools when a paused process or the Xcode view debugger can answer a visual UI question faster than more code probes.
Prefer evidence the agent can gather automatically first. If the user has to drive Xcode manually, ask for the minimum targeted output needed to answer one question.

## Breakpoints

Prefer precise breakpoints when you need one suspect path or mutation source.

Useful types:

- line breakpoints
- exception breakpoints
- symbolic breakpoints
- conditional breakpoints
- action breakpoints that log and continue

Questions they answer well:

- did execution reach this path?
- who called this mutation?
- what stack produced the bad state?

## LLDB

Common commands:

```text
po object
p value
bt
frame variable
thread backtrace
```

Use:

- `po` for object descriptions
- `p` for simple expressions
- `bt` for stack inspection
- `frame variable` for locals without editing code

## Good Uses

- catch one bad mutation without permanent logging
- stop at the exact call stack that produces impossible state
- verify whether a framework callback really fires in the order you expect

## Symbolic Breakpoints

Useful for framework-owned transitions or layout problems.

Examples:

- `UIViewAlertForUnsatisfiableConstraints`
- `-[CALayer layoutSublayers]`

Use broad symbolic breakpoints carefully. They can become noisy quickly.

## Xcode View Debugger

Use it when the issue looks like:

- wrong hierarchy ownership
- unexpected clipping ancestor
- misplaced constraints
- offscreen or overlapping views

It is especially useful after visual probes prove the bug exists but not which ancestor owns it.

Agents cannot inspect the Xcode view debugger directly unless the user provides screenshots or a targeted report from it. Treat it as a manual fallback, not the default first move.

If you ask the user to use it, request only the minimum useful output, for example:

- one screenshot of the selected suspect subtree
- the name of the clipping or masking ancestor
- the frame of one suspect view and its parent
- the one constraint or hierarchy relationship that would confirm the hypothesis

## Anti-Patterns

- setting a symbolic breakpoint so broad that it stops constantly without insight
- replacing all instrumentation with breakpoints when a few targeted screenshots would explain the issue better
- pausing in the debugger and assuming the paused hierarchy matches animated runtime behavior
- asking the user to explore the whole hierarchy manually when a targeted probe or one specific view-debugger check would do
