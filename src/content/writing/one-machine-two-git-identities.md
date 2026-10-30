---
title: One machine, two Git identities
description: Separate SSH keys and commit identities for personal and work accounts, without logging in and out of anything.
date: 2026-10-05
tags: ['git', 'ssh']
---

If you use one laptop for a personal GitHub account and a work GitLab account, the naive setup
ends with commits attributed to the wrong person and pushes rejected for `Permission denied
(publickey)`. The fix is small: one key per identity, an SSH alias for each, and per-repository
git config.

## Keys

One key per identity, named so you can tell them apart later:

```bash
ssh-keygen -t ed25519 -C "personal@email.com"   # -> ~/.ssh/id_ed25519_personal
ssh-keygen -t ed25519 -C "work@company.com"     # -> ~/.ssh/id_ed25519_gitlab_work
```

Load both into the agent:

```bash
eval "$(ssh-agent -s)"
ssh-add ~/.ssh/id_ed25519_personal
ssh-add ~/.ssh/id_ed25519_gitlab_work
ssh-add -l
```

Upload each public key (`.pub`, not the private one) to its own provider.

## Aliases

`~/.ssh/config`:

```ssh
Host github-personal
    HostName github.com
    User git
    IdentityFile ~/.ssh/id_ed25519_personal
    IdentitiesOnly yes

Host gitlab-work
    HostName gitlab.com
    User git
    IdentityFile ~/.ssh/id_ed25519_gitlab_work
    IdentitiesOnly yes
```

```bash
chmod 600 ~/.ssh/config
```

`IdentitiesOnly yes` is the line that stops the agent from cheerfully offering the wrong key
first. Without it, "which key did it use" becomes a guessing game.

Test:

```bash
ssh -T git@github-personal
ssh -T gitlab-work
```

## Cloning through an alias

The host in the clone URL is what selects the key, so use the alias instead of the real hostname:

```bash
git clone git@github-personal:fahmi/some-project.git
git clone gitlab-work:company/backend.git
```

## Identity per repository

Keys authenticate you; they do not set who the commit says you are. Do that per repository:

```bash
git config user.name "Personal Name"
git config user.email "personal@email.com"
```

The habit worth forming is never setting `user.email` globally on a machine that touches more
than one account — a single forgetful `--global` is how work commits end up on your personal
profile.

## When it goes wrong

- **`Permission denied (publickey)`** — `ssh-add -l` first. An empty list means the agent has no
  keys in this shell.
- **Clone asks for the wrong account** — the URL still says `github.com` rather than
  `github-personal`.
- **Config ignored entirely** — permissions on `~/.ssh/config` are too open; `chmod 600` it.

Once this is in place the daily workflow stops involving accounts at all: you clone with an
alias, and the right key and the right identity follow.
