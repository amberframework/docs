# Debugging Crystal Projects

This tutorial have some tips and tricks to debug Crystal projects with tools like GDB or LLDB using debugger clients like [Native Debug](https://marketplace.visualstudio.com/items?itemName=webfreak.debug) for VSCode.

## Requisites

* Crystal - See instalation guide [here](https://crystal-lang.org/docs/installation/)
* Amber Project - See installation guide [here](/getting-started/Installation/README.md)
* GNU Debugger (GDB) or LLVM Debugger (LLDB)
* VSCode + [Crystal Support](https://marketplace.visualstudio.com/items?itemName=faustinoaq.crystal-lang) + [Native Debug](https://marketplace.visualstudio.com/items?itemName=webfreak.debug)

To install GDB and LLDB according with your OS use one of the following commands:

##### MacOS

`brew install gdb lldb`

##### Ubuntu

`apt install gdb lldb`

##### Fedora

`dnf install gdb lldb`

##### ArchLinux

`pacman -S gdb lldb`


> Note: Before continuing setting up the debugger make sure you have the above requisites install. These settings have been verified for a MacOS and Linux's enviroments

## Steps

### 1. `task.json` configuration to compile a crystal project

```json
{
  "version": "2.0.0",
  "tasks": [
    {
      "label": "Compile",
      "command": "shards build --debug ${workspaceFolderBasename}",
      "type": "shell"
    }
  ]
      }
```

### 2. `launch.json` configuration to debug a binary

#### Using GDB

```json
{
  "version": "0.2.0",
  "configurations": [
    {
      "name": "Debug",
      "type": "gdb",
      "request": "launch",
      "target": "./bin/${workspaceFolderBasename}",
      "cwd": "${workspaceRoot}",
      "preLaunchTask": "Compile"
    }
  ]
}
```

#### Using LLDB

```json
{
  "version": "0.2.0",
  "configurations": [
    {
      "name": "Debug",
      "type": "lldb-mi",
      "request": "launch",
      "target": "./bin/${workspaceFolderBasename}",
      "cwd": "${workspaceRoot}",
      "preLaunchTask": "Compile"
    }
  ]
}
```

### 3. Then hit the DEBUG green play button

![debugging](https://i.imgur.com/GsGT1h0.png)

![green button](https://camo.githubusercontent.com/30adba87add4770abf2c3982206748123f8a2c6e/687474703a2f2f692e696d6775722e636f6d2f6d674b41366d782e706e67)

![change variable value](https://camo.githubusercontent.com/c5a551366c3eb2464c920bf3f95e8cdfb97ad827/687474703a2f2f692e696d6775722e636f6d2f6b506b546e75442e706e67)

[Native Debug](https://github.com/WebFreak001/code-debug) allows to set breakpoints, watch variables and execute GDB commands inside VSCode.



