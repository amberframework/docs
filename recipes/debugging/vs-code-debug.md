# Debugging Crystal Projects using VSCode

This tutorial has tips and tricks on how to debug Crystal projects. It shows how to leverage tools like GDB or LLDB using debugger clients like [Native Debug](https://marketplace.visualstudio.com/items?itemName=webfreak.debug) for VSCode.

## Prerequisites

* Crystal - See instalation guide [here](https://crystal-lang.org/docs/installation/)
* Crystal project - See installation guide [here](https://crystal-lang.org/docs/using_the_compiler/#creating-a-project-or-library) or [here (Amber)](/getting-started/Installation/README.md)
* VSCode with [Crystal Lang](https://marketplace.visualstudio.com/items?itemName=faustinoaq.crystal-lang) and [Native Debug](https://marketplace.visualstudio.com/items?itemName=webfreak.debug) extensions
* GNU debugger (GDB) or LLVM debugger (LLDB) - See installation guide below

Install GDB or LLDB based commands below for your OS.

##### MacOS

`brew install gdb lldb`

##### Ubuntu

`apt install gdb lldb`

##### Fedora

`dnf install gdb lldb`

##### ArchLinux

`pacman -S gdb lldb`


> Note: Confirm that the above prerequisites are installed before setting up the debugger. These settings have been verified for a MacOS and Linux's environments.

## Steps

> By convention the project directory name is the same as your application name, if you have changed it, please update ${workspaceFolderBasename} with the name configured inside shards.yml

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

## Tips and Tricks for debugging Crystal applications

> DISCLAIMER: LLDB does not show data for variables in crystal applications yet, see [issue #4457](https://github.com/crystal-lang/crystal/issues/4457).

Fully debugging Crystal applications is not supported yet. You can use some of the techniques below to improve the debugging experience.

### 1. Use debugger keyword

Instead of putting breakpoints using commands inside GDB or LLDB you can try to set a breakpoint using `debugger` keyword.

```crystal
i = 0
while i < 3
  i += 1
  debugger # => breakpoint
end
```

### 2. Avoid breakpoints inside blocks

Currently, Crystal lacks support for debugging inside of blocks. If you put a breakpoint inside a block, it will be ignored.

As a workaround, use `pp` to pretty print objects inside of blocks.

```crystal
3.times do |i|
  pp i
end
# i => 1
# i => 2
# i => 3
```

### 3. Try `@[NoInline]` to debug arguments data

Sometimes crystal will optimize argument data, so the debugger will show `<optimized output>` instead of the arguments. To avoid this behavior use the `@[NoInline]` attribute before your function implementation.

```crystal
@[NoInline]
def foo(bar)
  debugger
end
```

### 4. Printing strings objects (GDB)

To print string objects in the debugger:

First, setup the debugger with the `debugger` statement:

```crystal
foo = "Hello World!"
debugger
```

Then use `print` in the debugging console.

```sh
(gdb) print &foo.c
$1 = (UInt8 *) 0x10008e6c4 "Hello World!"
```

Or add `&foo.c` using a new variable entry on watch section in VSCode debugger

![watch](https://i.imgur.com/EpQinL7.png "Using VSCode GUI")

### 5. Printing array variables

To print array items in the debugger:

First, setup the debugger with the `debugger` statement:

```crystal
foo = ["item 0", "item 1", "item 2"]
debugger
```

Then use `print` in the debugging console:

```
(gdb) print &foo.buffer[0].c
$19 = (UInt8 *) 0x10008e7f4 "item 0"
```

Change the buffer index for each item you want to print.


### 6. Printing instance variables

For printing `@foo` var in this code:

```crystal
class Bar
  @foo = 0
  def baz
    debugger
  end
end

Bar.new
```

You can use `self.foo` in the debugger terminal or VSCode GUI.

### 7. Print hidden objects

Some objects do not show at all. You can unhide them using the `.to_s` method and a temporary debugging variable, like this:

```crystal
def bar(hello)
  "#{hello} World!"
end

def foo(hello)
  bar_hello_to_s = bar(hello).to_s
  debugger
end

foo("Hello")
```

This trick allows showing the `bar_hello_to_s` variable inside the debugger tool.
