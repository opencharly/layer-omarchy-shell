# omarchy-shell

Omarchy's desktop shell as a charly layer — the Quickshell bar, notifier,
launcher, OSD and lockscreen, plus the tools its indicators drive.

The `omarchy-shell` meta installs nothing itself. Composing it yields the
supervised Quickshell service running Omarchy's own QML tree
(`omarchy-quickshell`) and the command-line tools its volume, brightness, power
and disk indicators shell out to (`omarchy-osd`).

In Omarchy 4.x the whole shell is **one Quickshell tree** shipped by the
`omarchy` package at `/usr/share/omarchy/shell`. There is no waybar, mako, walker
or swayosd to install — that was the 3.x stack and it is gone from the 4.x base
set.

## Using it

Pin **only the meta**, at its sub-path:

```yaml
candy:
    - '@github.com/opencharly/layer-omarchy-shell/candy/omarchy-shell:v2026.243.1941'
```

The meta names its members by bare sibling name, and `QualifyRemoteSiblingDeps`
rewrites those to `.../candy/<member>` at this repo's own tag — so one pin pulls
both at a matching version.

| Member | Role |
|---|---|
| `omarchy-quickshell` | Quickshell itself plus the supervised `omarchy-shell` service |
| `omarchy-osd` | the tools the shell's indicators shell out to (`pamixer`, `brightnessctl`, `udiskie`, `powerprofilesctl`, `aether`) |

The service runs at priority 14 — after the compositor (12), before the streaming
transport (18) — and waits on the compositor's **client** socket `wayland-2`
(the stack runs two compositors; `wayland-1` is the streaming parent, and nothing
creates `wayland-0`). `pod-dbus` is a `require:`: without a session bus
Quickshell starts but disables its bluetooth and NetworkManager modules.

## How to use it

Compose the meta in a desktop box's `candy:` list — the `omarchy-cstream` image
does exactly this:

```yaml
omarchy-cstream:
  candy:
    base: omarchy
    candy:
      - '@github.com/opencharly/layer-omarchy-shell/candy/omarchy-shell:v2026.243.1941'
      # ... hyprland, terminal, themes and fonts layers
```

## Layout

- `charly.yml` — repo shape: the `discover:` rule that finds the member candies.
- `candy/omarchy-shell/` — the meta candy + its `skill:` entity.
- `candy/omarchy-quickshell/` — the supervised Quickshell service candy.
- `candy/omarchy-osd/` — the indicator-backend tools candy.
- `.github/workflows/tag-on-merge.yml` — CalVer tag + `CHANGELOG/` on merge.
- `README.md` — this user overview.

## Related

- Owning skill: `/charly-distros:omarchy-shell` — the single QML tree, why the
  service does not use `omarchy-launch-shell`, and why `pod-dbus` is
  load-bearing.
- Compositor: `/charly-pod:hyprland`.
- Streaming desktop: `/charly-distros:omarchy-cstream`.
- [`opencharly/opencharly](https://github.com/opencharly/opencharly) — the umbrella.
