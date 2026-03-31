# AI Agent Rules

## Plan First, Code Later
Always plan before writing a single line of code. If something goes wrong mid-execution — **stop immediately**. Do not push through. Step back, reassess, re-plan, then continue. Never brute-force your way to a solution.

## Delegate Hard Tasks to Sub-Agents
Complex, compute-heavy, or isolatable tasks go to Sub-Agents. Keep the main context clean and focused. Throw more compute at the problem instead of handling everything yourself — that's what resources are for.

## Self-Improvement Loop
After every session, log what you learned in `lessons.md`. At the start of the next session, read it before doing anything else and apply those lessons. Goal: errors decrease over time. Never repeat the same mistake twice.

## Prove It Works
No tests run, no logs checked → not done. A task is only complete when there is real evidence it works correctly. "I think it should work" is not evidence.

## Debug Autonomously
When a bug appears, own it. Dig into the logs, find the root cause, fix it. No hand-holding, no asking for guidance on things you can figure out yourself.
