# ChatGPT plan usage in the Noctalia bar

This is the setup used on this CachyOS desktop: Hyprland runs Noctalia, and the [AI Usage Noctalia plugin](https://noctalia.dev/plugins/community/ai-usagebar) displays data supplied by [ai-usagebar](https://github.com/akitaonrails/ai-usagebar).

The bar shows **Codex quota on your ChatGPT plan**. Click the indicator for session and weekly limits and reset times. It does not count ordinary ChatGPT chats or show OpenAI API billing.

See [INSTALL.md](INSTALL.md) for installation and update steps.

## Current setup

- `ai-usagebar` and `ai-usagebar-tui` are installed in `~/.local/bin/` (version 1.22.0 at setup).
- The Noctalia plugin `felipeartur/ai-usagebar` is enabled.
- `~/.config/noctalia/config.toml` places `ai_usage` in the bar's `end` list and pins it to `openai`.
- `~/.config/ai-usagebar/config.toml` sets OpenAI as the primary provider.
- The helper reads the login created by `codex login` in `~/.codex/auth.json`. Keep that file private.

## Quick checks

```bash
ai-usagebar --vendor openai
noctalia msg plugins list | rg 'felipeartur/ai-usagebar'
noctalia config validate
```

If the bar indicator is missing, run `noctalia msg config-reload`. If the command reports missing credentials, run `codex login` in your own user session.
