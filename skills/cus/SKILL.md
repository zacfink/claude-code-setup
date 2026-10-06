---
name: cus
description: Use when the user says "close up shop", "cus", "wrap up", "that's it for today" or types /cus — end-of-session routine that writes the session back into the ~/Notes brain and pushes it.
---

# Close up shop

Write today's session back into the brain, then stop. Don't start new work.

1. **Session note** — `~/Notes/sessions/YYYY-MM-DD-<slug>.md` from `~/Notes/.brain/session-template.md`
   (frontmatter: date, title, projects, status, cwd). TL;DR, What happened, Decisions, Open threads.
   Facts only from this session. If a note for today already exists from this session, add to it;
   never rewrite older session notes.
2. **Project pages** — for each project touched: current state, open threads, `updated:` date.
   Learned something about a person → their `people/` page. Course work → the course's Weekly file
   (`Confused about` / `Flagged for the exam` are worth filling).
3. **now.md** — true, punchy headline + dek, add to Done, keep ≥5 dated Upcoming items.
4. **Nudges** — add any real promise/deadline left open to `nudges.md`. Never re-add a `status: no` one.
5. **Build + push** — `python3 ~/Notes/.brain/build.py`, then in `~/Notes`:
   `git add -A && git commit -m "<session title>" && git push` (Bash needs `allowed_domains: ["github.com"]`).
6. **Report** — 2–3 lines: what was saved, anything left open for tomorrow.
