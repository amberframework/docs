# VS Code Amber Debug

Debugging an Amber project on VSCode with GDB and [Native Debug](https://github.com/faustinoaq/vscode-crystal-lang#67-debugging).

## Requisites

* Crystal
* Amber Project
* GNU Debugger \(GDB\)
* VSCode + [Crystal Support](https://marketplace.visualstudio.com/search?term=tag%3Acrystal&target=VSCode&category=All%20categories&sortBy=Relevance) + [Native Debug](https://github.com/faustinoaq/vscode-crystal-lang#67-debugging)

## Steps

#### 1. **`task.json`to compile the main file with debug support**

```json
{
  "version": "2.0.0",
  "tasks": [
    {
      "taskName": "amber",
      "command": "crystal build -d -p src/app.cr",
      "type": "shell",
      "presentation": {
        "echo": true,
        "reveal": "never",
        "focus": false,
        "panel": "shared"
      }
    }
  ]
}
```

#### 2.**`launch.json`configuration to debug binary**

```json
{
  "version": "0.2.0",
  "configurations": [
    {
      "name": "Debug",
      "type": "gdb",
      "request": "launch",
      "target": "./app",
      "cwd": "${workspaceRoot}",
      "preLaunchTask": "amber"
    }
  ]
}
```

#### 3. Then hit the green play button and whoala![![](https://camo.githubusercontent.com/30adba87add4770abf2c3982206748123f8a2c6e/687474703a2f2f692e696d6775722e636f6d2f6d674b41366d782e706e67 "set breakpoint")](https://camo.githubusercontent.com/30adba87add4770abf2c3982206748123f8a2c6e/687474703a2f2f692e696d6775722e636f6d2f6d674b41366d782e706e67)[![](https://camo.githubusercontent.com/c5a551366c3eb2464c920bf3f95e8cdfb97ad827/687474703a2f2f692e696d6775722e636f6d2f6b506b546e75442e706e67 "change variable value")](https://camo.githubusercontent.com/c5a551366c3eb2464c920bf3f95e8cdfb97ad827/687474703a2f2f692e696d6775722e636f6d2f6b506b546e75442e706e67)[Native Debug](https://github.com/faustinoaq/vscode-crystal-lang#67-debugging) allows to set breakpoints, watch variables and execute GDB commands inside VSCode.



