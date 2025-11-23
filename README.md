# Loci Syntax for VS Code

Syntax highlighting for the Loci DSL used to describe rules, data dependencies, and computation steps in Loci-based applications.

## Features
- Highlights Loci directives and declarations such as `$include`, `$type` (directive), type names, storage kinds (`store`, `param`, `const_store`, etc.), and `$rule` forms (`pointwise`, `singleton`, `apply`, `unit`, `default`, `optional`).
- Distinguishes rule modifiers and helpers: `constraint(...)`, `conditional(...)`, `option(...)`, `inplace(...)`, `prelude`/`postlude`, Loci reduction tags like `[Loci::Summation]`, and constants like `EMPTY`/`UNIVERSE`.
- Marks `$`-prefixed variables (including indexed forms like `$temperature{n=0}`) and Loci namespace calls (`Loci::load_module`, `Loci::makeQuery`, etc.).
- Supports `//` and `/* */` comments, string and numeric literals, and the `<-ci->`, `<-`, `->` arrows used in rule heads.
- Loci scopes run first; unmatched content falls back to the built-in C++ grammar (especially inside `{ ... }` blocks) so mixed Loci/C++ stays readable without losing DSL highlighting.

## Install
1. Install dependencies: `npm install`
2. Build: `npm run compile` (or `npm run watch` while developing)
3. Package (optional): `npx @vscode/vsce package` then `code --install-extension loci-syntax-*.vsix`
4. Or run in an Extension Development Host with `F5` from VS Code.

## Run in VS Code
- Open this folder in VS Code.
- Press `Run and Debug` (or F5) and pick **Run Extension**; a new Extension Development Host window opens with the Loci syntax loaded.
- Open a `.loci` file there (e.g., from `/home/wandadar/software/loci/...`) and confirm the status bar shows **Loci DSL**.
- Colors come from your current VS Code theme; this extension just provides scopes.

## Use
- Open a `.loci` file; VS Code should select **Loci DSL** automatically. If not, switch the language mode manually.
- The grammar ships with this extension (`syntaxes/loci.tmlLanguage.json`); no extra configuration is required.
- Colors come from your current VS Code theme; this extension just provides scopes.

## Customize Loci colors (no GUI clicking)
- Open your user settings JSON directly (no UI): run `Preferences: Open User Settings (JSON)` from the Command Palette, or edit the file on disk:
  - Linux: `$HOME/.config/Code/User/settings.json`
  - macOS: `$HOME/Library/Application Support/Code/User/settings.json`
  - Windows: `%APPDATA%\\Code\\User\\settings.json`
- Add or merge this block (pure JSON, copy/paste safe) to force specific Loci token styling:
```json
"editor.tokenColorCustomizations": {
  "textMateRules": [
    {
      "scope": "keyword.control.rule-statement.loci",
      "settings": { "fontStyle": "bold" }
    },
    {
      "scope": "support.function.rule-kind.loci",
      "settings": { "fontStyle": "italic" }
    },
    {
      "scope": "entity.name.function.rule-output.loci",
      "settings": { "foreground": "#ffcc66" }
    },
    {
      "scope": "variable.parameter.rule-input.loci",
      "settings": { "foreground": "#82aaff" }
    }
  ]
}
```
- VS Code accepts comments in `settings.json`, but if your editor flags them, keep the snippet above as-is (no `//` comments) and ensure it sits inside the top-level `{ ... }`. If you already have `editor.tokenColorCustomizations`, just merge the `textMateRules` entries.
- Adjust colors to taste; these scopes come from `syntaxes/loci.tmlLanguage.json` and override your theme for Loci files.

## Rule anatomy (quick reference)
- `$rule` keyword and rule kind (`pointwise`, `singleton`, etc.).
- Rule head: outputs before the rightmost `<-`, inputs after it, arrow highlighted separately.
- Trailing modifiers: `option(...)`, `constraint(...)`; `prelude`, `compute` markers.

## Example
```loci
$include "flowPsi.lh"

$type solution store<real> ;
$type stop_iter param<int> ;

$rule default(stop_iter) { $stop_iter = 1000 ; }

$rule pointwise(dtcfl<-dtmax) {
  $dtcfl = real($dtmax) ;
}

$rule apply(cl->qresidual<-qdot)[Loci::Summation],
  constraint(cl->geom_cells) {
  join($cl->$qresidual, $qdot) ;
}
```

## Contributing
- Add or adjust scopes in `syntaxes/loci.tmlLanguage.json`.
- Update `language-configuration.json` if bracket or comment behavior changes.
- Provide example snippets/screenshots in this README to showcase improvements.

## Release Notes
- 0.1.0 — Expand grammar coverage, add language activation, and document install/usage.
- 0.0.1 — Initial scaffold.
