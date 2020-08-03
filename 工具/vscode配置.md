## VSCode配置

```json
{
  // **************** 基础设置 ********************
  "workbench.colorTheme": "Material Theme Palenight",
  "workbench.iconTheme": "eq-material-theme-icons-palenight",
  "workbench.startupEditor": "newUntitledFile",
  "window.zoomLevel": 1,
  "breadcrumbs.enabled": false,
  "explorer.confirmDelete": false,
  "files.autoGuessEncoding": true,
  "files.insertFinalNewline": true,
  "files.autoSave": "onFocusChange",
  "files.associations": {
    "*.vue": "vue",
    "*.wpy": "vue",
    "*.wxml": "html",
    "*.wxss": "css"
  },
  // 这些文件不会显示到工作区
  "files.exclude": {
    "**/.git": true,
    "**/.svn": true,
    "**/.hg": true,
    "**/CVS": true,
    "**/.DS_Store": true,
    "**/node_modules": false,
    "**/.idea": true,
    "**/.classpath": true,
    "**/.project": true,
    "**/.settings": true,
    "**/.factorypath": true
  },
  "editor.tabSize": 4,
  "editor.fontLigatures": false,
  "editor.fontFamily": "FiraCode Nerd Font Mono",
  "editor.fontWeight": "normal",
  "editor.fontSize": 14,
  "editor.lineHeight": 24,
  "editor.lineNumbers": "on",
  "editor.tabCompletion": "on",
  "editor.formatOnSave": false,
  "editor.rulers": [],
  "editor.codeActionsOnSave": {
    "source.organizeImports": true
  },
  // 当为on时将在视野的宽度处换行
  "editor.wordWrap": "off",
  "editor.detectIndentation": false,
  "editor.dragAndDrop": false,
  "editor.minimap.enabled": false,
  "editor.renameOnType": true,
  "editor.largeFileOptimizations": false,
  "editor.suggestSelection": "first",
  // **************** 调试设置设置 ********************
  "debug.console.fontFamily": "FiraCode Nerd Font Mono",
  "debug.console.fontSize": 14,
  "debug.console.lineHeight": 24,
  // **************** 终端设置 ********************
  "terminal.integrated.shell.windows": "/Applications/iTerm.app",
  "terminal.integrated.fontFamily": "FiraCode Nerd Font Mono ",
  "terminal.integrated.fontSize": 14,
  "terminal.integrated.lineHeight": 1.25,
  // **************** GIT设置 ********************
  "git.enableSmartCommit": true,
  "git.autofetch": true,
  "git.confirmSync": false,
  // **************** EMMET设置 ********************
  "emmet.triggerExpansionOnTab": true,
  "emmet.showAbbreviationSuggestions": true,
  "emmet.showExpandedAbbreviation": "always",
  "emmet.includeLanguages": {
    "vue-html": "html",
    "vue": "html",
    "wpy": "html"
    // "javascript": "javascriptreact"
  },
  // **************** 搜索设置 ********************
  "search.exclude": {
    "**/node_modules": true,
    "**/bower_components": true,
    "**/target": true,
    "**/logs": true
  },
  // **************** vue设置 ********************
  "vetur.format.defaultFormatter.html": "prettier",
  "vetur.format.defaultFormatter.js": "prettier",
  // **************** Sync设置 ********************
  "sync.gist": "9e53cbbff59a4ead0d19d4fb29d71142",
  // **************** Prettier 设置 ********************
  "prettier.configPath": "~/.prettierrc.js",
  // ****************  在线服务支持 ********************
  "typescript.disableAutomaticTypeAcquisition": true,
  "liveServer.settings.donotShowInfoMsg": true,
  // ****************  REST请求设置 ********************
  "rest-client.fontSize": 14,
  "rest-client.fontFamily": "FiraCode Nerd Font Mono ",
  // ****************  Java配置 ********************
  "[java]": {
    "editor.defaultFormatter": "redhat.java"
  },
  "java.home": "/Library/Java/JavaVirtualMachines/jdk1.8.0_251.jdk/Contents/Home",
  "java.help.firstView": "gettingStarted",
  "java.semanticHighlighting.enabled": true,
  "java.requirements.JDK11Warning": false,
  "java.jdt.ls.vmargs": "-XX:+UseParallelGC -XX:GCTimeRatio=4 -XX:AdaptiveSizePolicyWeight=90 -Dsun.zip.disableMemoryMapping=true -Xmx1G -Xms100m -javaagent:\"/Users/inconspicuousy/.vscode/extensions/gabrielbb.vscode-lombok-1.0.1/server/lombok.jar\"",
  "java.debug.settings.enableRunDebugCodeLens": false,
  // ****************  其他配置 ********************
  "vsintellicode.modify.editor.suggestSelection": "automaticallyOverrodeDefaultValue",
  // ****************  JSON配置 ********************
  "[jsonc]": {
    "editor.defaultFormatter": "esbenp.prettier-vscode",
    "editor.tabSize": 2,
    "editor.formatOnSave": true
  },
  "[json]": {
    "editor.defaultFormatter": "esbenp.prettier-vscode",
    "editor.tabSize": 2,
    "editor.formatOnSave": true
  },
  // ****************  JS配置 ********************
  "[javascript]": {
    "editor.defaultFormatter": "esbenp.prettier-vscode",
    "editor.tabSize": 2,
    "editor.formatOnSave": true
  },
  // ****************  CSS配置 ********************
  "[css]": {
    "editor.defaultFormatter": "esbenp.prettier-vscode",
    "editor.tabSize": 2,
    "editor.formatOnSave": true
  },
  // ****************  HTML配置 ********************
  "[html]": {
    "editor.defaultFormatter": "esbenp.prettier-vscode",
    "editor.tabSize": 2,
    "editor.formatOnSave": true
  },
  "rsp-ui.enableStartServerOnActivation": [
    {
      "id": "redhat.vscode-community-server-connector",
      "name": "Community Server Connector",
      "startOnActivation": true
    },
    {
      "id": "redhat.vscode-server-connector",
      "name": "Red Hat Server Connector",
      "startOnActivation": true
    }
  ],
  // ****************  quarkus 配置 ********************
  "quarkus.tools.alwaysShowWelcomePage": false,
  // ****************  LeetCode 配置 ********************
  "leetcode.defaultLanguage": "java",
  "leetcode.endpoint": "leetcode-cn",
  "leetcode.hint.configWebviewMarkdown": false
}
```

