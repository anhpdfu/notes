### settings.json

```json
{
  "workbench.colorTheme": "Cobalt2",
  "editor.fontFamily": "'Fira Code', 'Courier New', monospace",
  "editor.fontLigatures": true,
  "editor.fontSize": 14,
  "editor.lineHeight": 23,
  "editor.letterSpacing": 0.5,
  "editor.fontWeight": "400",
  "editor.cursorStyle": "block",
  "editor.cursorWidth": 10,
  "editor.cursorBlinking": "smooth",
  "editor.renderWhitespace": "all",
  "editor.tabSize": 2,
  "editor.insertSpaces": true,
  "editor.rulers": [
    100,
    140
  ],
  "files.trimTrailingWhitespace": true,
  "prettier.eslintIntegration": true,
  "workbench.startupEditor": "none",
  "terminal.integrated.shell.windows": "C:\\Program Files\\Git\\bin\\bash.exe",
  "terminal.integrated.cursorBlinking": true,
  "terminal.integrated.fontSize": 13,
  "git.autofetch": true
}
```

### Extensions

- wesbos.theme-cobalt2-2.1.6 --- Cobalt2 Theme Official (Wes Bos)
- esbenp.prettier-vscode-1.5.0 --- Prettier - Code formatter (Esben Petersen)
- octref.vetur-0.12.5 --- Vetur (Pine Wu)
- msjsdiag.debugger-for-chrome-4.7.0 --- Debugger for Chrome (Microsoft)
- Dracula Official (Dracula Theme)
- Docker (Microsoft)
- wayou.vscode-todo-highlight-1.0.4 --- TODO Highlight (Wayou Liu)
- dbaeumer.vscode-eslint-1.8.0 --- ESLint (Dirk Baeumer)


```json
{
  "editor.renderWhitespace": "all",
  "editor.rulers": [
    100,
    140
  ],
  "[javascript]": {
    "editor.tabSize": 2,
  },
  "files.exclude": {
    "node_modules/": false,
    "package-lock.json": false,
    ".vscode": false
  }
}
```

.eslintrc.js
```js
module.exports = {
    "env": {
        "browser": true,
        "commonjs": true,
        "es6": true
    },
    "extends": "eslint:recommended",
    "parserOptions": {
        "ecmaFeatures": {
            "jsx": true
        },
        "ecmaVersion": 2018,
        "sourceType": "module"
    },
    "rules": {
        "linebreak-style": [
            "error",
            "windows"
        ],
        "quotes": [
            "error",
            "single",
        ],
        "semi": [
            "error",
            "always"
        ]
    }
};
```
