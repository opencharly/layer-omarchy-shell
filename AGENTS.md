# AGENTS.md — layer-omarchy-shell

Standalone candy repo for Omarchy's desktop-shell layer — the Quickshell bar,
notifier, launcher, OSD and lockscreen (one QML tree), plus the tools its
indicators drive. The repo is multi-candy: the root `charly.yml` carries only the
repo shape (`discover:`), and the member candies live in
`candy/<name>/charly.yml`.

Canonical files:

- `charly.yml` — the repo shape (`repo:` + `discover:`).
- `candy/omarchy-shell/charly.yml` — the meta candy and its
  `omarchy-shell-skill:` skill entity (projected as
  `/charly-distros:omarchy-shell`).
- `candy/omarchy-quickshell/charly.yml` — the supervised Quickshell service.
- `candy/omarchy-osd/charly.yml` — the indicator-backend tools.
- `.github/workflows/tag-on-merge.yml` — CalVer tag + `CHANGELOG/` on merge.
- `README.md` — user overview only; never agent guidance.

## Load these skills first (R0)

- `/charly-distros:omarchy-shell` — the owning skill. The single QML tree, why
  the service does not use `omarchy-launch-shell`, why `pod-dbus` is
  load-bearing, and why the bar is a layer-shell surface (not a toplevel). Load
  before editing or troubleshooting.
- `/charly-distros:omarchy-base` — the family foundation skill.
- `/charly-image:layer` — the candy authoring reference (`charly.yml` schema,
  `plan:` step verbs incl. `check:`, per-distro `distro:` arms, package/repo
  sections, and service declarations). Load before editing any entity field or
  plan step.

## Build / validate / test

- The merge gate is the **org-wide** `charly/pr-validator` (required check
  `validate / validate`, defined in `opencharly/.github`); this repo has **no
  per-repo candy gate** and ships only `.github/workflows/tag-on-merge.yml`.
- The members' `plan:` `check:` steps are the functional evidence — the meta's
  check asserts the shell runtime and indicator tools coexist; the service's
  checks assert the binary, the tree, the supervised RUNNING state (a bounded
  poll, not a sleep) and a stable uptime.

## Modify this repo

- Members live in `candy/<name>/charly.yml`, not inline in the root manifest.
- The service execs `quickshell` directly, not `omarchy-launch-shell`: a pod has
  neither a systemd user manager nor a journald socket for `systemd-cat`. Keep
  `pod-dbus` a `require:` — without a session bus two bar widgets are dead with
  no other symptom.
- Keep `XDG_RUNTIME_DIR=/tmp/cstream-rt` and `WAYLAND_DISPLAY=wayland-2` in the
  **service** `env:`, never as an image-wide candy `env:` block.
- Edit the meta's `skill:` entity together with its candy entity; the skill is
  the projected usage source.
- New behaviour claims belong in the `plan:` as an observable `check:` step, and
  in the skill body.

## Landing

- The authoritative rulebook is the umbrella `AGENTS.md` in
  `opencharly/opencharly` and `charly/AGENTS.md` in the charly repo — read it
  before landing.
- Release history lives in `CHANGELOG/`.
