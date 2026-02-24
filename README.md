# QuickShell Hyprland Bar

A lightweight top bar for [QuickShell](https://quickshell.org/) focused on Hyprland workflows.

This setup provides:
- Dynamic Hyprland workspace indicators with local-workspace plugin integration
- Active window title in the center
- Contextual media display + transport popup controls
- Audio widget with wheel volume, plus output/mic popup sliders
- Keyboard lock-state indicators (Caps/Num)
- Click-to-toggle clock/date
- Power menu (suspend/reboot/poweroff/lock/logout)

## Layout

Per monitor, the bar is split into three zones:

- Left:
  - `Workspaces`
  - `Media` (only visible when something is actively playing)
- Center:
  - `WindowTitle`
- Right:
  - `Volume`
  - `KeyboardState`
  - `Clock`
  - `SystemPower`

## Component Behavior

### Workspaces

- Polls Hyprland JSON state via:
  - `hyprctl workspaces -j`
  - `hyprctl monitors -j`
  - `hyprctl clients -j`
- Shows only occupied workspaces on the current monitor.
- Highlights active workspace.
- Shows a `S` button when a special workspace has windows.
- Clicking `S` toggles special workspace `magic`.

### Hyprland Local Workspace Plugin Support

Workspace switching is integrated with `hypr-local-workspaces`:

- For numeric workspace labels, clicking a workspace runs:
  - `hyprctl dispatch focusmonitor '<monitor>'`
  - `hypr-local-workspaces goto <n> --no-compact`
- Non-numeric labels fall back to native Hyprland workspace dispatch.

This gives monitor-local workspace navigation behavior instead of global-only workspace targeting.

### Media

- Uses `playerctl` and only shows when status is `Playing`.
- Pill text preference:
  - `artist - title`
  - fallback to `title`
  - fallback to `artist`
  - fallback to player name
- Click opens popup with:
  - metadata block
  - icon transport controls: previous, play/pause, stop, next

### Volume

- Shows output + input percentage and muted state.
- Mouse wheel over the volume pill adjusts output volume in steps.
- Click opens popup with two vertical sliders:
  - Left: output volume
  - Right: mic/input volume
- Slider interaction sets volume and unmutes target automatically.

### Keyboard State

- Reads Caps Lock / Num Lock state from `/sys/class/leds/*::capslock/brightness` and `/sys/class/leds/*::numlock/brightness`.

### Clock

- Updates every second.
- Click toggles between:
  - time (`%I:%M:%S %p`)
  - date (`%A, %B %-d, %Y`)

### System Power

- Click opens menu actions:
  - Suspend
  - Restart
  - Power Off
  - Lock Screen
  - Log Out

## Dependencies

Required commands/services:
- `quickshell`
- `hyprctl`
- `hypr-local-workspaces` (plugin command used for local workspace switching)
- `wpctl` (PipeWire/WirePlumber)
- `playerctl`
- `systemctl`
- `date`
- standard shell tools (`sh`, `awk`, `sed`, `grep`, `cut`, etc.)

Optional but recommended:
- `hyprlock` (for lock-screen action in power menu)

## Installation

1. Place this config at:
   - `~/.config/quickshell`
2. Ensure dependencies above are installed and in `PATH`.
3. Start QuickShell:
   - `quickshell`

To reload quickly:
- `~/.config/quickshell/reload-quickshell.sh`

## Hyprland Layer Rules (Optional)

A sample ruleset is included in:
- `hyprland-layer-config.conf`

Copy or merge relevant rules into your `~/.config/hypr/hyprland.conf` for better layer animation/blur behavior.

## Customization

Theme and sizing tokens live in:
- `config/Colors.qml`
- `config/AppStyle.qml`

Adjust these for:
- spacing/radius/font sizes
- component colors/icons
- polling intervals
- popup sizes and offsets
- wheel step amount (`volumeWheelStepPercent`)

## Notes

- This config is monitor-aware and spawns one bar per screen.
- Workspace display is intentionally occupancy-driven (only occupied workspaces are shown).
- Media visibility is intentionally strict: only active `Playing` sessions are displayed.
