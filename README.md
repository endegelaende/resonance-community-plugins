# Resonance Community Plugins

[![Update Index](https://github.com/resonance-plugins/community/actions/workflows/update-index.yml/badge.svg)](https://github.com/resonance-plugins/community/actions/workflows/update-index.yml)

Community plugin repository for the [Resonance](https://github.com/endegelaende/resonance-server) music server.

Plugins listed here appear in the **Available** tab of the Resonance Plugin Manager UI and can be installed with a single click.

---

## 📦 Available Plugins

No community plugins published yet — be the first! See [Contributing](#-contributing) below.

---

## 🏗️ How It Works

```
                ┌─────────────────────┐
                │  Plugin Author      │
                │  pushes tag v1.2.3  │
                └────────┬────────────┘
                         │
                         ▼
                ┌─────────────────────┐
                │  GitHub Actions     │
                │  build-release.yml  │
                │  • zip plugin       │
                │  • compute SHA-256  │
                │  • create Release   │
                └────────┬────────────┘
                         │
                         ▼
                ┌─────────────────────┐
                │  GitHub Actions     │
                │  update-index.yml   │
                │  • scan all plugins │
                │  • generate index   │
                │  • deploy to Pages  │
                └────────┬────────────┘
                         │
                         ▼
                ┌─────────────────────┐
                │  GitHub Pages       │
                │  index.json         │
                │  (public URL)       │
                └────────┬────────────┘
                         │
                         ▼
                ┌─────────────────────┐
                │  Resonance Server   │
                │  fetches index.json │
                │  shows "Available"  │
                │  installs plugin    │
                └─────────────────────┘
```

### Repository Structure

```
community/
├── .github/
│   └── workflows/
│       ├── build-release.yml    # Builds ZIP + SHA for tagged releases
│       └── update-index.yml     # Generates index.json → GitHub Pages
├── plugins/
│   └── your-plugin/             # One folder per plugin
│       ├── plugin.toml          # Plugin manifest (name, version, etc.)
│       ├── __init__.py          # Plugin source code
│       └── ...
├── index.json                   # Auto-generated plugin index (do not edit)
└── README.md
```

### Index Format

The `index.json` served via GitHub Pages has this structure:

```json
{
  "version": 1,
  "generated": "2025-01-15T12:00:00Z",
  "plugins": [
    {
      "name": "my-plugin",
      "version": "1.0.0",
      "description": "A cool community plugin",
      "author": "Your Name",
      "category": "tools",
      "icon": "",
      "min_resonance_version": "0.1.0",
      "url": "https://github.com/endegelaende/resonance-community-plugins/releases/download/my-plugin-v1.0.0/my-plugin-1.0.0.zip",
      "sha256": "abc123...",
      "homepage": "https://github.com/endegelaende/resonance-community-plugins/tree/main/plugins/my-plugin",
      "changelog": "",
      "tags": ["utility"]
    }
  ]
}
```

---

## 🚀 Installing Plugins

### Via the Web UI

1. Open the Resonance web interface
2. Navigate to **Settings → Plugins → Available**
3. Click **Install** on the plugin you want
4. Restart Resonance if prompted

### Via JSON-RPC

```json
{
  "method": "slim.request",
  "params": ["-", ["pluginmanager", "installrepo", "name:my-plugin"]]
}
```

---

## 🤝 Contributing

We welcome community plugin contributions! Here's how to add your plugin:

### Requirements

- A valid `plugin.toml` manifest with all required fields
- A working `setup()` function in `__init__.py`
- Clean `teardown()` — no resource leaks
- Compatibility with the current Resonance release
- No malicious code, no telemetry without consent

### Steps to Submit a Plugin

1. **Fork** this repository
2. **Create** a new folder under `plugins/your-plugin-name/`
3. **Add** your plugin code with a valid `plugin.toml`:

   ```toml
   [plugin]
   name = "your-plugin-name"
   version = "1.0.0"
   description = "Short description of what it does"
   author = "Your Name"
   min_resonance_version = "0.1.0"
   category = "music"  # music, radio, podcast, tools, misc
   ```

4. **Test** your plugin locally by placing it in `data/installed_plugins/` of your Resonance server
5. **Open a Pull Request** with a clear description of your plugin

### Plugin Guidelines

- **Naming**: Use lowercase, hyphen-separated names (e.g., `my-cool-plugin`)
- **Versioning**: Follow [SemVer](https://semver.org/) (MAJOR.MINOR.PATCH)
- **Settings**: Use `SettingDefinition` for user-configurable options — avoid hardcoded values
- **Logging**: Use `logging.getLogger(__name__)` — never `print()`
- **Dependencies**: Avoid heavy external dependencies; if needed, document them clearly
- **Security**: Never store credentials in plain text; use the plugin settings API with `masked=True`

### Categories

| Category | Use for |
|----------|---------|
| `music` | Music discovery, metadata, lyrics, etc. |
| `radio` | Internet radio sources |
| `podcast` | Podcast directories and tools |
| `tools` | Utilities, maintenance, integrations |
| `misc` | Everything else |

---

## 🔧 For Maintainers

### Creating a Release

Releases are created automatically when you push a version tag:

```bash
# After merging a plugin update:
git tag my-plugin-v1.0.0
git push origin my-plugin-v1.0.0
```

The `build-release.yml` workflow will:
1. Package the plugin directory into a ZIP
2. Compute the SHA-256 checksum
3. Create a GitHub Release with the ZIP attached

The `update-index.yml` workflow then:
1. Scans all `plugins/*/plugin.toml` files
2. Matches them with the latest GitHub Release
3. Generates `index.json`
4. Deploys to GitHub Pages

### Manual Index Rebuild

```bash
# Trigger via GitHub Actions UI or:
gh workflow run update-index.yml
```

---

## 📄 License

Community plugins are contributed under their respective licenses as specified by each plugin author. The repository infrastructure (workflows, scripts) is licensed under the [MIT License](LICENSE).

---

## 🔗 Links

- [Resonance Server](https://github.com/endegelaende/resonance-server) — the main server project
- [Plugin API Documentation](https://github.com/endegelaende/resonance-server/blob/main/docs/) — how to write plugins
- [Plugin System Architecture](https://github.com/endegelaende/resonance-server/blob/main/docs/PLUGIN_UPGRADE.md) — technical details