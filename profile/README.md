# Introduction

![icon](https://github.com/user-attachments/assets/0f54c9cb-a5cd-449b-9a32-22c81870ffc2)

This organization hosts many [coc.nvim](https://github.com/neoclide/coc.nvim)
extensions. Most of them are transplanted from responding
[VS Code](https://github.com/microsoft/vscode) extensions.
In order to transplant them, the following attempts were made:

## Transplant

### Macro Preprocess

The first attempt comes from
[vscode-ltex](https://github.com/valentjn/vscode-ltex/blob/3d0cb8cd9b4d0dc8ef6c08a4f376767820678cf6/src/extension.ts#L8-L14):

```javascript
// #if TARGET == 'vscode'
import * as Code from 'vscode';
import * as CodeLanguageClient from 'vscode-languageclient/node';
// #elseif TARGET == 'coc.nvim'
// import * as Code from 'coc.nvim';
// import CodeLanguageClient = Code;
// #endif
```

They create
[patchForTarget.py](https://github.com/valentjn/vscode-ltex/blob/3d0cb8cd9b4d0dc8ef6c08a4f376767820678cf6/tools/patchForTarget.py)
to preprocess the source code and generate the target code for coc.nvim from
vscode extension. C preprocessor `gcc -E` is also usable.
For [example](https://github.com/tlahmann/vscode-alex/pull/14/changes):

```javascript
//# #if HAVE_VSCODE
import { workspace, ExtensionContext } from 'vscode';
import * as vscode from 'vscode';
//# #elif HAVE_COC_NVIM
//# import { workspace, ExtensionContext } from 'coc.nvim';
//# import * as vscode from 'coc.nvim';
//# #endif
```

However, this solution is not perfect:

- requires a lot of changes to the source code
- not easy to maintain source code for coc.nvim

- [ ] [coc-nix](https://github.com/nix-community/vscode-nix-ide/pull/439)
- [ ] [coc-alex](https://github.com/tlahmann/vscode-alex/pull/14)
- [ ] [coc-mlir](https://github.com/llvm/vscode-mlir/pull/11)
- [ ] [coc-bitbake](https://github.com/yoctoproject/vscode-bitbake/pull/178)
- [ ] [coc-meson](https://github.com/mesonbuild/vscode-meson/pull/272)
- [ ] [coc-awk](https://github.com/Beaglefoot/awk-language-server/pull/68)
- [ ] [coc-variables](https://github.com/DominicVonk/vscode-variables/pull/4)
- [ ] [coc-graphviz](https://github.com/joaompinto/vscode-graphviz/pull/40)

### Runtime Check

A new solution is check the runtime environment and load the corresponding module.
For [example](https://github.com/TravisTheTechie/vscode-write-good/pull/24/changes):

```javascript
import type {
    ExtensionContext as ExtensionContext_vscode,
    TextDocument as TextDocument_vscode,
    Diagnostic as Diagnostic_vscode,
    DiagnosticCollection as DiagnosticCollection_vscode,
} from 'vscode';
import type {
    ExtensionContext as ExtensionContext_coc,
    TextDocument as TextDocument_coc,
    Diagnostic as Diagnostic_coc,
    DiagnosticCollection as DiagnosticCollection_coc,
} from 'coc.nvim';
type ExtensionContext = ExtensionContext_vscode | ExtensionContext_coc;
type TextDocument = TextDocument_vscode | TextDocument_coc;
type Diagnostic = Diagnostic_vscode | Diagnostic_coc;
type DiagnosticCollection = DiagnosticCollection_vscode | DiagnosticCollection_coc;
let vscode;
try {
    vscode = require('vscode');  // eslint-disable-line
} catch (error) {
    vscode = require('coc.nvim');  // eslint-disable-line
}
const workspace = vscode.workspace;
const languages = vscode.languages;
```

However, this solution is not perfect either:

- requires a lot of changes to the source code
- need many `// eslint-disable-line`
- language server cannot work for type annotation

- [ ] [write-good-linter](https://github.com/TravisTheTechie/vscode-write-good/pull/24)
- [x] [hyuga-vscode-client](https://github.com/sakuraiyuta/hyuga/pull/25)
- [x] [vscode-jq](https://github.com/wader/vscode-jq/pull/8)

### JIT Convert

Refer [coc-vscode-loader](https://github.com/coc-plugin/coc-vscode-loader).

## Discussions

Welcome to join the discussions!

- [Transplant VS Code extension](https://github.com/neoclide/coc.nvim/discussions/5668)
- [Host coc.nvim extensions](https://github.com/neoclide/coc.nvim/discussions/5593)
