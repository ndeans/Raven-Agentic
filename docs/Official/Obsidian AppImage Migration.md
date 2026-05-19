# Obsidian AppImage Migration

**Date:** 2026-04-23  
**Reason:** Enable agent access to system keyring and `gh` CLI from within Claudian

---

## The Problem We Were Solving

The Pierre agent (running inside Claude Code, inside Obsidian) needed to interact with GitHub directly — specifically to add items to the Raven project board. This requires `gh`, the GitHub CLI tool. `gh` stores its login token in the **system keyring** (KWallet5 on KDE) — the same secure password store that remembers other system passwords.

The problem: Pierre couldn't reach the keyring. Every attempt to use `gh` from inside Claudian failed silently.

---

## Why It Was Failing — The Flatpak Sandbox

Obsidian was installed as a **Flatpak**. Flatpak is a Linux packaging system built around security isolation — a Flatpak app runs inside a sandbox that restricts what it can access.

One of those restricted services is **D-Bus** — the message bus that Linux desktop applications use to talk to each other. KWallet5 (the keyring) communicates over D-Bus. Inside the Flatpak sandbox, D-Bus access is proxied and restricted. Even with the keyring running and the `gh` token stored in it, the sandboxed Obsidian couldn't reach KWallet5 to retrieve the token.

Larry (running in Warp, outside any sandbox) could use `gh` without issue. Pierre couldn't, because Pierre runs *inside* Obsidian.

---

## The Solution — AppImage

An **AppImage** is a completely different packaging format. Instead of a sandboxed installation, an AppImage is a single self-contained file — like a `.exe` on Windows — that contains the entire application. When launched, it runs as a **normal native process** with the same access to system services as any other application.

No sandbox. No proxy. No restrictions.

### What Was Done

1. Downloaded `Obsidian-1.12.7.AppImage` from Obsidian's GitHub releases to `~/Applications/`
2. Made it executable (`chmod +x`)
3. Created `~/.local/share/applications/obsidian.desktop` for KDE launcher and icon integration
4. Backed up vault registrations and settings from Flatpak config (`~/.var/app/md.obsidian.Obsidian/config/obsidian/`) to the native config location (`~/.config/obsidian/`)
5. Launched the AppImage — all three vaults present, everything intact

### Launch Command

```bash
~/Applications/Obsidian-1.12.7.AppImage &>/dev/null & disown
```

The `& disown` detaches Obsidian from the terminal so the shell stays free.

---

## How Obsidian Now Reaches the Keyring

```
KWallet5 (keyring)
    ↕  D-Bus (system message bus)
Obsidian AppImage  ←— native process, full D-Bus access
    └── Claude Code (Pierre)
            └── gh CLI
                    └── GitHub API
```

Because Obsidian is now a native process, it has normal D-Bus access. When Pierre runs `gh`, it connects to KWallet5 over D-Bus, retrieves the stored GitHub token, and authenticates. The whole chain works because there is no sandbox in the way.

---

## Token Scope Fix

After migration, `gh` could authenticate but couldn't access the GitHub Projects API. The existing token was missing the `project` permission scope.

Fix:
```bash
gh auth refresh --scopes "admin:public_key,gist,read:org,repo,project"
```

This opened a browser device-flow authorization. GitHub issued an updated token, stored back in KWallet5 automatically. Pierre can now post to the project board directly.

---

## Result

| Before | After |
|---|---|
| Obsidian = Flatpak (sandboxed) | Obsidian = AppImage (native) |
| `gh` inaccessible from Pierre | `gh` works from Pierre |
| No GitHub board access from Claudian | Full GitHub board access |
| Token missing `project` scope | Token has all required scopes |

---

## Cleanup (Pending)

The Flatpak is still installed but dormant. Once the AppImage proves stable, remove it:

```bash
flatpak uninstall md.obsidian.Obsidian
```

User data at `~/.var/app/md.obsidian.Obsidian/` is preserved by default (not deleted on uninstall).
