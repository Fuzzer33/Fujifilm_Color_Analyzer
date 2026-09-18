# Latent (Fujifilm Color Analyzer)

Recipe-aware, local-only photo library for Fujifilm shooters. Full design and roadmap live in `latent-plan.md` (~25 KB). It is not auto-loaded, so read the relevant section only when a task needs it.

## Current state

@PROJECT_STATE.md

## Session protocol

**Start of session:** the state above is already loaded. Open by stating, in two or three lines, the current phase and the first item under "Next up". Do not re-read `latent-plan.md` in full unless the task needs it.

**During the session:** when a task finishes, a decision is made, or the plan changes, update `PROJECT_STATE.md` right away. Do not wait for the end, because sessions often stop without warning.

**End of session:** when the user says they are done, wrapping up, or runs `/wrap-up`, update `PROJECT_STATE.md` before replying:
1. Move finished items to "Done" with the date.
2. Rewrite "In progress" and "Next up" so a cold start can resume from them.
3. Record new decisions and open questions.
4. Add one line to "Session log" and trim it to the last 5 entries.

Keep `PROJECT_STATE.md` under about 100 lines. It is a handoff note, not a history. Put detail in the plan or in code comments.

## Conventions

- Photos are the only color in the UI. Use hueless neutral grays for all chrome (OKLab a = b = 0).
- The app is read-only toward original RAF/JPEG files. All state lives in its own database and cache.
- Fujifilm only. Skip and count other cameras' files, and never half-support them.
