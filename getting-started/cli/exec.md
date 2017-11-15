# Exec Command

The `exec` command allows you to run short pieces of code within the amber application
scope. Code can be specified in the following three ways:
1. *inline mode*: inline code specified in the command line as a string argument e.g.: `amber exec User.first`
2. *file mode*: code within an existing .cr file specified as a path e.g.: `amber exec scripts/stuff.cr`
3. *editor mode*: a terminal-based code editor is opened and the resulting code is executed once you save and exit e.g.: `amber exec`... edit code in Vim... code runs.

Here is a list of the commands available:

```
amber exec [OPTIONS] [CODE | FILE_PATH]

Arguments:
  FILE_PATH  Path to a .cr file to run in the application scope (file mode)
  CODE       String containing valid crystal code to run in the application scope (inline mode)

  Note: specifying no argument activates editor mode

Options:
  -e, --editor       Prefered Editor: [vim, nano, pico, etc] (default: vim)
  -b, --back [n]     Load the nth previous script (only valid in editor mode)
```

## Inline Mode
Inline mode allows you to specify a string as an argument to `amber exec` containing
valid crystal code to be executed within the application scope. The string must either
contain no spaces or be enclosed within single or double quotes. Backslashes may be
used to escape any problematic quotes that need to appear within your code.

Example:
```
$ amber exec "User.find_by(:email, \"test@example.com\")"
#<User:0x168df80 @id=23 @email=test@example.com...>
```

## File Mode
File mode is similar to inline mode except the argument passed to `amber exec` is
interpreted as a path to a .cr file. For file mode to activate, the argument must
be a string ending in .cr, and must correspond with a file that exists on the file system,
within the current project.

Example:
```
$ amber exec cool_script.cr
cool stuff happened ^_^
```

## Editor Mode
Editor mode is activated by entering `amber exec` with no arguments. By default,
editor mode will open the Vim text editor (this can be overridden by specifying your
preferred text editor as the `-e` option). Once you are done editing your code, you
can quit without saving to abort, or you can save and quit, which will cause amber
to run the code. Specifying a value for the `--back` option allows you to go _n_
scripts back and edit+run that script.

Example:
```
$ amber exec -e nano -b 1
(nano opens the previous script that was edited + run in editor mode)
(output of the script is printed to the terminal once you save + quit)
```
