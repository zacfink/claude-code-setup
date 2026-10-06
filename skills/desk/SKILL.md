---
name: desk
description: Use when a task needs your Mac itself — clicking, typing into a form or app, or seeing what is on screen — and there is no CLI, API or MCP route. Drives the mouse and keyboard with the `desk` command (screenshot, click, type, key, scroll, open).
---

# desk: use your Mac

`desk` (from [screen-intelligence](https://github.com/zacfink/screen-intelligence), `screen_intelligence/desk.py`, installed on PATH) gives you
eyes and hands. You are the vision model and the planner; it only captures and acts.

Every `desk` call needs `dangerouslyDisableSandbox: true` (the sandbox blocks screen capture and input).

## Commands

Look with the cheapest tool that answers the question:

| Command | Does | Cost |
|---|---|---|
| `desk ui --find TEXT [App]` | Only the elements whose label contains TEXT. Use this to locate a known button or field. | ~40 tokens |
| `desk ui [--full] [App]` | The window's buttons, fields, links and labels as numbered lines (first 80; `--all` for everything). | ~700 tokens |
| `desk shot` | Screenshot to `runtime/desk.png` (1280 wide, faint grid, red circle at the mouse); then Read it. For apps that draw their own pixels (the `ui` says "nothing readable"), or to see layout, colours and images. | ~1,200 tokens |

`[App]` is the name as macOS shows it (`Safari`, `Google Chrome`, `Finder`); without it, the frontmost app, which is usually the terminal.

Act:

| Command | Does |
|---|---|
| `desk click #N` | Click element N from the last `ui`. Brings its app to the front first, since the terminal often covers it. |
| `desk click X Y [right\|double]` | Click at (X, Y) in the last screenshot's pixels. |
| `desk drag X1 Y1 X2 Y2` | Press, move slowly, release (screenshot pixels, or `#N` for either end). |
| `desk type "text"` · `desk key cmd+l` · `desk scroll down 5` · `desk open "App"` · `desk wait 0.5` | |
| `desk until "log out\|logout" Safari --timeout 900` | Wait (checking every 2s) until a label or the window title contains any of the texts; `--gone` waits for it to disappear. For handing off to the user. |
| `desk run "click #4; type Ada; key tab; type Lovelace; ui --find Submit Safari"` | Several steps in one call, ending with a cheap look. |

## Loop

1. Look with `ui --find` or `ui` first; `shot` only when text isn't enough.
2. Act. Batch the dull runs (tabbing through form fields, typing known values) with `run`, and end the batch with a look.
   Take risky or uncertain clicks one at a time.
3. Check the result before going on. If it didn't do what you expected, look again (a screenshot if the text is ambiguous); don't click harder.

Element numbers only stay valid until the page changes; after any click that navigates or opens something, run `ui` again.
Prefer the keyboard when it's reliable (`tab` between fields, `cmd+l` for the address bar).

## Hand off to the user (logins, 2FA, captchas)

Get the user to the right screen first (open the URL, click "Log in"). Then start
`desk until "<something only visible once they're done>" <App> --timeout 900` with `run_in_background: true`, and in the
same turn tell them exactly what to do ("log in with your school account; I'll carry on when you're in"). When the
background task finishes, carry on without waiting for them to say so. Pick a marker that can't appear on the
login page itself: "Log out" or their name, not "Dashboard".

## Ask the user first

Stop and ask in chat before any click that:
- submits a form or application, sends a message or email, posts, or buys anything;
- deletes, overwrites, or changes a setting or permission;
- answers a legal or eligibility question (work authorization, attestations, privacy consent) — those are their answers;
- needs a password, 2FA code or payment detail — never type these; hand back to them.

Fill only facts from `~/Notes` or that the user gave in this conversation. Leave anything else blank and list it for them.
Captchas are theirs.

Before starting, tell the user you're taking the mouse, since they may be using the machine. They can abort any time by
slamming the mouse into a screen corner (pyautogui's fail-safe).

Prefer a real route over the GUI when one exists: a CLI, an API, an MCP tool, or `open <url>`.
