# VS Code Amber Debug

Debugging an Amber project on VSCode with GDB and [Native Debug](https://github.com/crystal-lang-tools/vscode-crystal-lang/wiki/Useful-extensions#debugging).

## Requisites

* Crystal
* Amber Project - See installation guide [here](/getting-started/Installation/README.md)
* GNU Debugger (GDB) - MacOS install `brew install gdb`
* VSCode + [Crystal Support](https://marketplace.visualstudio.com/items?itemName=faustinoaq.crystal-lang) + [Native Debug](https://github.com/WebFreak001/code-debug)

> Note: Before continuing setting up the debugger make sure you have the above requisites install. These settings have been verified for a MacOS enviroment.

## Steps

#### 1. `task.json`**to compile the main file with debug support**

```json
{
  "version": "2.0.0",
  "tasks": [
    {
      "taskName": "amber",
      "command": "crystal build -d -p src/{project file}.cr -o app",
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

#### 2.`launch.json`**configuration to debug binary**

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

#### 3. Then hit the green play button

![green button](https://camo.githubusercontent.com/30adba87add4770abf2c3982206748123f8a2c6e/687474703a2f2f692e696d6775722e636f6d2f6d674b41366d782e706e67)

![change variable value](https://camo.githubusercontent.com/c5a551366c3eb2464c920bf3f95e8cdfb97ad827/687474703a2f2f692e696d6775722e636f6d2f6b506b546e75442e706e67)

[Native Debug](https://github.com/WebFreak001/code-debug) allows to set breakpoints, watch variables and execute GDB commands inside VSCode.



