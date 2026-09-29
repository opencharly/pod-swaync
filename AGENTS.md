# AGENTS.md — pod-swaync

Standalone candy repo for the `swaync` candy — the SwayNotificationCenter
notification daemon for wlroots compositors, with a staged Catppuccin theme and
a wayland-aware wrapper run under supervisord. The candy lives in `charly.yml`
at the repo root plus its config, style, and wrapper.

Canonical files:

- `charly.yml` — the `swaync:` candy entity (description, `require`, `distro`,
  `service`, `plan`) and its `skill:` entity.
- `config.json`, `style.css` — the staged `~/.config/swaync` theme.
- `swaync-wrapper` — the launcher copied into `~/.local/bin`.
- `.github/workflows/tag-on-merge.yml` — CalVer tag + `CHANGELOG/` on merge.
- `CHANGELOG/` — per-CalVer history.
- `README.md` — user overview only; never agent guidance.

## Load these skills first (R0)

- `/charly-selkies:swaync` — the owning skill: the candy properties, the D-Bus
  auto-activation fix, and the waybar notification module. Load before editing,
  building, deploying, or troubleshooting this candy.
- `/charly-selkies:waybar` — the notification bell module consumer.
- `/charly-infrastructure:dbus-layer` — the D-Bus session-bus dependency.
- `/charly-check:dbus` — the `dbus:` check verb used to test notification
  delivery.
- `/charly-image:layer` — the candy authoring reference (`charly.yml` schema,
  `plan:` step verbs incl. `run:` / `copy:` / `check:`, service declarations).
- `/charly-check:check` — the check/R10 framework (`charly check box`,
  `charly check run <bed>`).
- `/charly-internals:git-workflow` — before any git/PR action.

## Build / validate / test

- `charly box validate` at the repo root — the structural check: the manifest
  must parse and validate at the installed charly.
- The merge gate is the **org-wide** `charly/pr-validator` (required check
  `validate / validate`, defined in `opencharly/.github`); this repo has **no**
  per-repo candy gate. Its only workflow file is
  `.github/workflows/tag-on-merge.yml`.
- The candy's own `check:` steps assert the `swaync` daemon and `swaync-client`
  CLI, the staged config and style, the installed wrapper, the removed D-Bus
  auto-activation units, and the resolved `SwayNotificationCenter` package.

## Modify this repo

- Edit the `swaync:` candy entity in `charly.yml`; the `skill:` entity in the
  same file is the owning skill's source — a candy change and its skill change
  land together.
- Keep the D-Bus auto-activation removal (`org.erikreider.swaync.service`,
  `org.erikreider.swaync.cc.service`) in the `plan:`; without it D-Bus spawns a
  competing instance and the supervisord service goes FATAL.
- Keep the service `scope: system` and priority 14 (after the compositor, before
  waybar) in step with the wrapper's socket wait.
- The `skill:` entity is the source for `/charly-selkies:swaync`; never edit the
  generated `SKILL.md` — regenerate it.

## Landing

- PR-only. Every change lands through a pull request; the org-required
  `charly/pr-validator` validates the diff and body and arms native auto-merge on
  PASS. Direct pushes to `main` are blocked.
- History lives in `CHANGELOG/` (written by `tag-on-merge` at merge time); the PR
  body IS the changelog.
- The authoritative rulebook is the umbrella `AGENTS.md` in
  `opencharly/opencharly` and `charly/AGENTS.md` in the charly repo. Do not
  restate its rules here.
