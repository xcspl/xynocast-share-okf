---
type: Playbook
title: Maintaining and distributing xynocast-share-okf
description: How this bundle is curated from xynocast-okf, edited, exported for external recipients, and how to reset its git history.
tags:
- xynocast
- maintenance
- distribution
timestamp: '2026-08-03'
status: active
---

# Maintaining and distributing xynocast-share-okf

Policy — what may and must never appear here — lives in
`xynocast-okf/sops/external-knowledge-sharing.md`. This doc is the mechanics:
who edits, how it's exported, how history gets reset.

## Editing

- This is a real, git-backed repo (`xcspl/xynocast-share-okf`, private).
  Internal team members, including marketing, get normal collaborator
  write access and edit it directly like any other OKF bundle — house
  frontmatter (§2 of the guide), same validator.
- There is **no mechanical sync** from `xynocast-okf`. When company facts
  change (a new product ships, an office moves), update the relevant doc
  here by hand, using `xynocast-okf` as background reference. Reference,
  don't restate internal detail — this bundle only ever needs the
  public-facing version of a fact.

## Distributing to external recipients

**External prospects and clients never get repo access.** GitHub read
access is repo-scoped, not path-scoped — any collaborator sees the entire
commit history, forever, regardless of what's in it today. Instead, export
a snapshot with no `.git` and send that directly:

```bash
cd ~/local/okfs/xynocast-share-okf
git archive --format=zip -o /tmp/xynocast-share-okf-$(date +%Y-%m-%d).zip HEAD
```

Send the resulting zip via email or a drive link. Regenerate and resend on
each update — there is no live-pull relationship for external recipients.

## Resetting git history

Do this after finding something in history that shouldn't be there — a
leaked-in real client name, a pasted internal number, anything that
crosses the policy in `xynocast-okf/sops/external-knowledge-sharing.md`.
Resetting produces one fresh commit with the current, scrubbed content and
discards everything before it:

```bash
cd ~/local/okfs/xynocast-share-okf
git checkout --orphan scrubbed
git add -A
git commit -m "reset: scrub history"
git branch -D main
git branch -m main
git push -f origin main
```

**This only affects future clones and fetches.** Anyone who already cloned
or fetched before the reset keeps the old history locally — a reset is not
a retraction. Treat every commit as if it could eventually be seen; the
reset is a cleanup habit, not a safety net to lean on.

## Marketing-team access

Adding a marketing-team member as a collaborator is a manual step via
GitHub repo settings (Settings → Collaborators, or org team assignment) —
not something scripted here, since it needs a real GitHub account per
person. Do this per person as they need edit access.
