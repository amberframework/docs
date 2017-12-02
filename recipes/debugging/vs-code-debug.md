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

## Trips and Tricks for debugging Crystal applications

> DISCLAIMER: LLDB doesn't show variables data for crystal applications yet, see [issue #4457](https://github.com/crystal-lang/crystal/issues/4457).

Actually debugging Crystal application is not fully supported yet. However you can use some techniques to improve your debugging experience.

### 1. Use debugger keyword

Instead of putting breakpoints using commands inside GDB or LLDB you can try to set a breakpoint using `debugger` keyword.

```crystal
i = 0
while i < 3
  debugger # => breakpoint
  i += 1
end
```

### 2. Avoid breakpoints inside blocks

Currently crystal lacks of some debugging info, making blocks hard to debug, so if you try to put a breakpoint inside a block, it would be almost ignored.

Alternatively, you can try `pp` to pretty print objects.

```crystal
3.times do |i|
  pp i
end
# i => 1
# i => 2
# i => 3
```

### 3. Try `@[NoInline]` to debug arguments data

Sometimes crystal optimize arguments data, so the debugger will shows args like `<optimized output>`. To avoid this behaivior use `@[NoInline]` attribute before your function implementation.

```crystal
@[NoInline]
def foo(bar)
  debugger
end
```

### 4. Printing strings objects (GDB)

To print string objects like:

```crystal
foo = "Hello World!"
debugger
```

Use `print` in debugging console:

```sh
(gdb) print &foo.c
$1 = (UInt8 *) 0x10008e6c4 "Hello World!"
```

Or adding `&foo.c` using a new variable entry on watch section in VSCode debugger

![watch](https://i.imgur.com/EpQinL7.png)

### 5. Printing array variables

To print array items like:

```crystal
foo = ["item 0", "item 1", "item 2"]
debugger
```

Try in your debugger this command:

```
(gdb) print &foo.buffer[0].c
$19 = (UInt8 *) 0x10008e7f4 "item 0"
```

Changing the buffer index for each item.


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

You can use `self.foo` in debugger terminal or VSCode GUI.

### 7. Print hidden objects

Some objects doen't show at all. So you can unhide them by using `.to_s` method and an auxiliar variable, like this:

```crystal
def bar(hello)
  "#{hello} World!"
end

def foo(hello)
  auxiliar = bar(hello).to_s
  debugger
end

foo("Hello")
```

This trick allows to show auxiliar variable inside debugger tool.
