# Setup

How to rebuild this setup from nothing. Each step works on its own, so take only the parts you want.
macOS assumed; Brain also runs on Windows and Linux.

## 1. Notes: Brain

The notes folder is the memory everything else reads and writes.

```bash
mkdir -p ~/Notes && git clone https://github.com/zacfink/brain.git ~/Notes/.brain
ln -s ~/Notes/.brain/bin/brain ~/.local/bin/brain    # any folder on your PATH
brain                                                # opens the app at http://127.0.0.1:4747
```

Start the notes from the shapes in Brain's
[`FORMATS.md`](https://github.com/zacfink/brain/blob/main/FORMATS.md), copy its `example/` folder, or ask Claude
Code to draft them from your projects, calendar and email. Then make `~/Notes` its own **private** git repo and push
it, so the notes back up and the phone app can sync:

```bash
cd ~/Notes && git init && git add -A && git commit -m "notes"
gh repo create notes --private --source . --push
```

## 2. The session-start hook

This is what makes every session start warm. It prints your active habits, inbox captures and calibration score,
and, until today's plan exists in `now.md`, tells Claude to do the morning update first. It makes no model call.

In `~/.claude/settings.json`:

```json
"hooks": {
  "SessionStart": [{ "hooks": [{ "type": "command", "command": "python3 ~/Notes/.brain/session_start.py 2>/dev/null || true", "timeout": 5 }] }]
}
```

## 3. Standing instructions

Put this in `~/.claude/CLAUDE.md` or Claude's memory:

> `~/Notes` is my brain. Before project work, read `~/Notes/INDEX.md` and the project page. File any inbox
> captures into the right note. Follow `on` habits in `claude/habits.md`, and propose (never enable) new ones.
> After real work, write a session note, update the project page and `now.md`, run
> `python3 ~/Notes/.brain/build.py`, then commit and push `~/Notes`.

## 4. My skills

```bash
git clone https://github.com/zacfink/claude-code-setup.git /tmp/ccs
cp -R /tmp/ccs/skills/* ~/.claude/skills/
```

- **`cus`**: works as soon as Brain is set up. Say "close up shop" or `/cus` at the end of a session.
- **`wild`**: works anywhere. Say "go wild".
- **`brain-polish`**: only useful if you change Brain's UI.
- **`desk`**: needs the `desk` command from
  [screen-intelligence](https://github.com/zacfink/screen-intelligence). Clone it, `pip install -r requirements.txt`
  with Python 3.10, and put a small script named `desk` on your PATH that runs
  `python -m screen_intelligence.desk "$@"` from the repo folder. `desk` itself calls no model (Claude is the eyes),
  so it needs no API key. The first run asks for Screen Recording and Accessibility permission. Every `desk` call has
  to run outside Claude Code's sandbox, so you'll approve each one.

## 5. Plugins

In Claude Code:

```
/plugin install superpowers@claude-plugins-official
/plugin install imessage@claude-plugins-official

/plugin marketplace add DietrichGebert/ponytail
/plugin install ponytail@ponytail

/plugin marketplace add pbakaus/impeccable
/plugin install impeccable@impeccable

/plugin marketplace add typesafe-ai/skills
/plugin install typesafe@typesafe-ai
```

gstack installs as a skill folder: `git clone https://github.com/garrytan/gstack.git ~/.claude/skills/gstack`, then
follow its README.

Restart Claude Code after installing. Skip any you don't need; they're independent.

## 6. Connectors

Turn on Gmail, Google Calendar, Google Drive and Notion in Claude's connector settings (claude.ai → Settings →
Connectors). Claude Code picks them up from your account. Then add the outward-action rule to your instructions:

> Read freely. Before sending an email, creating an invite, posting, or anything else other people will see, show me
> and wait for my OK.

## 7. Optional: iMessage

The iMessage plugin lets Claude read and answer one allowlisted text thread, so you can run it from your phone while
the laptop sits at home. Run `/imessage:configure` and follow it. Allowlist only your own thread. Anyone on the list
can ask Claude to act as you.

## Check it works

1. Start a new Claude Code session. The first thing it does should be the morning update.
2. Do a bit of work, then say "close up shop". A new note should appear in `~/Notes/sessions/`, and `git log` in
   `~/Notes` should show the push.
3. Open Brain (`brain`) and find the session in the timeline.
