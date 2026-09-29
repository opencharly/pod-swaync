# pod-swaync

The `swaync` candy of the OpenCharly candy library, as a standalone repo (the
candy de-submodule cutover, kind-prefixed naming). It provides the
SwayNotificationCenter notification daemon for wlroots compositors (sway,
labwc).

## What it provides

Installs SwayNotificationCenter (the `swaync` daemon and the `swaync-client`
control CLI), stages a Catppuccin-themed `config.json` and `style.css` into the
user's `~/.config/swaync`, and installs a wayland-aware wrapper run as a
supervisord service. The build also removes the D-Bus auto-activation service
files so a single supervisord-managed instance owns the notification bus name.

| Property | Value |
|---|---|
| Service | `swaync` (`~/.local/bin/swaync-wrapper`, `restart: always`, priority 14) |
| Requires | `pod-dbus` |
| Install files | `swaync-wrapper`, `config.json`, `style.css` |
| Packages | `SwayNotificationCenter` (RPM), `swaync` (pac) |

The D-Bus auto-activation fix matters because containers use supervisord, not
systemd as PID 1: without removing the `org.erikreider.swaync*.service` files,
D-Bus spawns a competing `swaync` that steals the bus name and drives the
supervisord instance to FATAL.

## How to use it

Typically composed via the desktop stack rather than used directly:

```yaml
my-desktop:
  candy:
    - '@github.com/opencharly/pod-swaync:<tag>'
```

Test notifications declaratively with the `dbus:` check verb:

```bash
charly check live my-image --filter dbus
```

## Verification

The candy's `check:` plan asserts the `swaync` daemon and `swaync-client` CLI,
the staged `config.json` and `style.css`, the installed wrapper, the **removed**
D-Bus auto-activation units, and the resolved `SwayNotificationCenter` package
(via `package_map:`).

## Layout

- `charly.yml` — the `swaync:` candy entity (description, `require`, `distro`,
  `service`, `plan`) plus its `skill:` entity.
- `config.json`, `style.css`, `swaync-wrapper` — the staged theme and launcher.
- `.github/workflows/tag-on-merge.yml` — CalVer tag + `CHANGELOG/` on merge.
- `README.md` — this user overview.

## Related

- Owning skill: `/charly-selkies:swaync` — the candy properties, the D-Bus
  auto-activation fix, and the waybar notification module.
- `/charly-infrastructure:dbus-layer` — the D-Bus session-bus dependency.
- `/charly-selkies:waybar` — the notification bell module consumer.
- [`opencharly/charly`](https://github.com/opencharly/charly) — the charly CLI and image builder
