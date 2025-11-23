# Loci Syntax for VS Code

Syntax highlighting for the Loci DSL used to describe rules, data dependencies, and computation steps in Loci-based applications.

## What this extension highlights
- Loci directives and declarations like `$include`, `$type`, type names, storage kinds (`store`, `param`, `const_store`, etc.), and `$rule` forms (`pointwise`, `singleton`, `apply`, `unit`, `default`, `optional`).
- Rule modifiers and helpers: `constraint(...)`, `conditional(...)`, `option(...)`, `inplace(...)`, `prelude`/`postlude`, Loci reduction tags (`[Loci::Summation]`), constants like `EMPTY`/`UNIVERSE`.
- `$`-prefixed variables (including `$variable{n=0}`) and Loci namespace calls (`Loci::load_module`, `Loci::makeQuery`, etc.).
- Comments (`//`, `/* */`), string and numeric literals, and the `<-ci->`, `<-`, `->` arrows used in rule heads.
- Mixed Loci/C++: unmatched content falls back to the built-in C++ grammar, especially inside `{ ... }` blocks.

## Quick use
- Install the extension, open a `.loci` file, and confirm the status bar shows **Loci DSL**. Colors come from your theme; this extension provides scopes.
- If VS Code doesn’t auto-detect, switch the language mode to **Loci DSL** manually.

## Customize Loci colors (manual theme override)
- Open user settings JSON: `Preferences: Open User Settings (JSON)` or edit:
  - Linux: `$HOME/.config/Code/User/settings.json`
  - macOS: `$HOME/Library/Application Support/Code/User/settings.json`
  - Windows: `%APPDATA%\\Code\\User\\settings.json`
- Add or merge:
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
- Adjust colors to taste. These scopes come from `syntaxes/loci.tmlLanguage.json` and override your theme for Loci files.

## Rule anatomy (quick reference)
- `$rule` keyword and rule kind (`pointwise`, `singleton`, `apply`, `unit`, `default`, `optional`).
- Rule head: outputs before the rightmost `<-`, inputs after it; the arrow is highlighted separately.
- Trailing modifiers: `option(...)`, `constraint(...)`; markers like `prelude`, `compute`.

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

---

## Developer info (build, test, package)
- Prereqs: Node.js 20+ (current `@vscode/vsce` needs the global `File` API), npm. If you must stay on Node 18, use `npx @vscode/vsce@2.21.0 package` when packaging.
- Install deps: `npm install`
- Build once: `npm run compile` (or `npm run watch` while developing)
- Run in VS Code: open the folder, press **Run and Debug** (F5) and pick **Run Extension**; reload the Extension Development Host after edits. For a faster loop, run `npm run watch` in the main window while using F5 to reload.
- Package a VSIX (share/install locally):
```bash
npm install
npm run compile
npx @vscode/vsce package
```
  - Outputs `loci-syntax-<version>.vsix`; install via `code --install-extension loci-syntax-*.vsix`.
  - If `vsce` warns about a missing `repository` field, add your Git repo URL to `package.json` or pass `--allow-missing-repository`.

## Contributing
- Adjust scopes in `syntaxes/loci.tmlLanguage.json`.
- Update `language-configuration.json` if bracket or comment behavior changes.
- Provide example snippets/screenshots in this README to showcase improvements.

## Release Notes
- 0.1.0 — Expand grammar coverage, add language activation, and document install/usage.
- 0.0.1 — Initial scaffold.
