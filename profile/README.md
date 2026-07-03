<div align="center">
  <img width="192" height="192" alt="coc-plugin" src="https://github.com/user-attachments/assets/0f54c9cb-a5cd-449b-9a32-22c81870ffc2" />
</div>

<p align="center">
  Run <a href="https://github.com/microsoft/vscode">VS Code</a> extensions in <a href="https://github.com/neoclide/coc.nvim">coc.nvim</a> without source forks.
  <br>
  <a href="https://coc-plugin.github.io/coc-vscode-registry/"><b>Browse available extensions →</b></a>
</p>

---

## Quick start

```vim
:CocInstall coc-vscode-loader
:CocCommand loader.open
```

Browse, install, and manage extensions through the TUI — or define them in your config:

```vim
" .vimrc
let g:coc_loader_global_extensions = ['vscode-pyright', 'vscode-eslint']
```

## How it works

VS Code extensions consist of a thin API client and often a language server (LSP). Instead of patching source code by hand, `coc-vscode-loader` intercepts VS Code API calls at **install time** and rewrites them to coc.nvim equivalents. The extension runs unmodified.

The converter pipeline: **git clone → AST transforms → LanguageClient injection → npm install → esbuild → register to coc**.

5 step generators handle different plugin types (`language-client`, `source`, `bridge`, `snippets`, `mark-unsupported`), and 6 AST transforms handle API mapping (`import-mapping`, `class-to-factory`, `provider-register`, `enum-offset`, `language-client`, `strip-volar`).

**What works**: language servers (completion, diagnostics, formatting, hover, go-to-definition), direct-API plugins, snippets.

**Not yet supported**: extensions depending on VS Code-specific UI (webviews, panels, custom editors).

## Projects

### Core

- **[coc-vscode-loader](https://github.com/coc-plugin/coc-vscode-loader)** — Converter CLI + Plugin TUI. Converts VS Code extensions to coc.nvim plugins at install time, with a Mason-style package manager. Supports binary server download, Go/Rust source compilation, pip packages, and VSIX prebuilt extraction. Daily CI monitors 134 upstream repos for API-breaking changes.
- **[coc-vscode-registry](https://github.com/coc-plugin/coc-vscode-registry)** — Extension registry + browseable website. Each entry specifies source, plugin type, server kind, conversion steps, and system dependencies. See the [live site](https://coc-plugin.github.io/coc-vscode-registry/).

### Ported extensions

Converted VS Code extensions maintained under this organization:

**Language servers** — [coc-nix](https://github.com/coc-plugin/coc-nix) · [coc-mlir](https://github.com/coc-plugin/coc-mlir) · [coc-meson](https://github.com/coc-plugin/coc-meson) · [coc-bitbake](https://github.com/coc-plugin/coc-bitbake) · [coc-awk](https://github.com/coc-plugin/coc-awk) · [coc-graphviz](https://github.com/coc-plugin/coc-graphviz) · [coc-alex](https://github.com/coc-plugin/coc-alex) · [coc-write-good](https://github.com/coc-plugin/coc-write-good)

**Utilities** — [coc-variables](https://github.com/coc-plugin/coc-variables) · [coc-nerdfont](https://github.com/coc-plugin/coc-nerdfont) · [coc-emoji-shortcodes](https://github.com/coc-plugin/coc-emoji-shortcodes) · [coc-license](https://github.com/coc-plugin/coc-license)

## Adding extensions to the registry

The registry is community-driven. To add a new extension:

1. Check if the converter can handle it (most LSP-based extensions work out of the box)
2. Fork `coc-vscode-registry`, add an entry to `registry.json`
3. Run `npm run test:smoke` to validate the conversion
4. Open a PR

The registry update checker runs daily — if the upstream extension changes, an automated PR is created with the updated output.

## Links

- [Registry website](https://coc-plugin.github.io/coc-vscode-registry/)
- [Converter design docs](https://github.com/coc-plugin/coc-vscode-loader/tree/main/docs)
- [Discussion: Transplant VS Code extension](https://github.com/neoclide/coc.nvim/discussions/5668)
- [Discussion: Host coc.nvim extensions](https://github.com/neoclide/coc.nvim/discussions/5593)
