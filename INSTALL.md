# Install on CachyOS with Hyprland and Noctalia

These steps recreate the setup in [README.md](README.md). Run them as your normal desktop user. No Waybar or Omarchy installation is needed for this desktop, which uses Noctalia as its panel.

## 1. Install ai-usagebar

Download the latest official Linux x86-64 release and check its published SHA-256 before installing it in your user account:

```bash
version=$(curl -fsSL https://api.github.com/repos/akitaonrails/ai-usagebar/releases/latest | jq -r .tag_name)
archive=ai-usagebar-linux-x86_64.tar.gz
workdir=$(mktemp -d)
curl -fL "https://github.com/akitaonrails/ai-usagebar/releases/download/$version/$archive" -o "$workdir/$archive"
curl -fL "https://github.com/akitaonrails/ai-usagebar/releases/download/$version/$archive.sha256" -o "$workdir/$archive.sha256"
(cd "$workdir" && sha256sum -c "$archive.sha256")
tar -xzf "$workdir/$archive" -C "$workdir"
install -Dm755 "$workdir/ai-usagebar" "$HOME/.local/bin/ai-usagebar"
install -Dm755 "$workdir/ai-usagebar-tui" "$HOME/.local/bin/ai-usagebar-tui"
```

Make sure `~/.local/bin` is on the `PATH` of the Noctalia session. It already is on this machine. The [upstream install guide](https://github.com/akitaonrails/ai-usagebar#install) also offers the AUR package `ai-usagebar-bin` if you have an AUR helper.

## 2. Connect your ChatGPT plan

Sign in to the Codex CLI once if needed:

```bash
codex login
```

The helper reads the resulting `~/.codex/auth.json`. Do not copy credentials into the plugin configuration. To make OpenAI the default when you run the helper from a terminal, create `~/.config/ai-usagebar/config.toml`:

```toml
[ui]
primary = "openai"
```

## 3. Enable the Noctalia plugin

```bash
noctalia msg plugins enable felipeartur/ai-usagebar
```

This installs the [community plugin](https://noctalia.dev/plugins/community/ai-usagebar). In `~/.config/noctalia/config.toml`, add `ai_usage` to the existing `[bar.default]` `end` list, keeping its other entries. On this machine the list is:

```toml
[bar.default]
end = [ "media", "spacer_1", "ai_usage", "tray", "notifications", "network", "volume", "session" ]
```

Add the widget definition elsewhere in the same file:

```toml
[widget.ai_usage]
type = "felipeartur/ai-usagebar:bar"
vendor = "openai"
visualization = "gauge"
extras = "countdown"
```

Then load the edited bar configuration:

```bash
noctalia config validate
noctalia msg config-reload
```

The capsule shows usage and a reset countdown. Hover for limits; left click opens the detailed AI Usage panel; right click refreshes it. You can also open the panel from a terminal:

```bash
noctalia msg panel-toggle felipeartur/ai-usagebar:panel
```

## Update later

Repeat step 1 to update the helper to the latest release. Noctalia manages updates for enabled plugins from its community source; you can request one immediately with:

```bash
noctalia msg plugins update community
```

If the indicator has no data, check `ai-usagebar --vendor openai` and confirm `codex login` was run as the same desktop user. The reported percentages are Codex plan quota, not total ChatGPT message counts.
