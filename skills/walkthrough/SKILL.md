---
name: walkthrough
description: When explaining a diff, PR or set of changes that spans 3 or more locations, present it as a guided walkthrough inside the user's Emacs review viewer instead of a wall of text. Triggers on "walk me through", "explain this PR/diff/change", "tour of the changes".
---

# Walkthrough in the Emacs review viewer

Show the route as steps in the user's review viewer: each step highlights lines of the diff and explains them inline.

1. Pick the target:
   - a git range: `{"kind": "git-range", "directory": "/abs/repo", "range": "main...HEAD"}`. Use `"range": null` for uncommitted work.
   - a Forgejo PR: `{"kind": "forgejo", "host": "https://omphalos.io", "owner": "mr-x", "repo": "NAME", "number": N}`.
   - Omit `target` to use the review already open.
2. Write 3-10 steps in execution order to a temp JSON file:

   {"target": {...}, "steps": [{"path": "repo/relative/path", "side": "new", "line_start": 12, "line_end": 18, "title": "short title", "body": "1-3 sentences", "question": "one review question"}]}

   `side` is `new` unless the lines exist only on the old side. Line numbers are that side's real lines; each step must include at least one changed line.
3. Run (add `--socket-name=sandbox` when working on the sandbox):

   emacsclient --eval "(progn (require 'review-walkthrough) (review-walkthrough-start-file \"/tmp/FILE.json\"))"

4. Read the printed report:
   - `ok N of M steps`: done. Lines after it name rejected steps; fix and resend if they matter.
   - `retry: ...`: files are still loading; run the same command again after 5 seconds.
   - `error: ...`: fix what it says.
   - If emacsclient itself fails, give the walkthrough as text instead.
5. Afterwards tell the user only: the step count, `C-n`/`C-p` to move, `W` to end, and a one-line summary. Do not repeat the steps in chat.
