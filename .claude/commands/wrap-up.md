---
description: End-of-session handoff. Update PROJECT_STATE.md so the next session can resume cold.
---

Wrap up this session by updating `PROJECT_STATE.md`.

1. Run `git status` and `git diff --stat` to see what changed this session.
2. Update the file in place:
   - Set "Last updated" to today's date.
   - Move finished items to "Done" with the date.
   - Rewrite "In progress" (what is half-done, and exactly where it stopped) and "Next up" (ordered, concrete, first item startable without more context).
   - Add any new decisions or open questions. Remove ones that are now resolved.
   - Add one line to "Session log" and keep only the last 5.
3. Keep the file under about 100 lines. Cut stale detail instead of appending.
4. If the plan itself changed, make the matching edit in `latent-plan.md`.
5. Reply with a three-line summary of what you recorded. Do not commit unless the user asks.
