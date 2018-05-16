# Watch

The `amber watch` compiles and runs your project. It also monitors for changes and rebuilds when necessary.

Here is a list of the commands available:

`amber watch` or `amber w`

```bash
amber watch [OPTIONS]

Options:
  -b, --build             Overrides the default build command
                          (default: crystal build ./src/[process_name].cr)
  --build-args            Specifies arguments for the build command
  -i, --info              Shows the values for build/run commands, build/run args, and watched files
  -n, --name              Sets the name of the app process
                          (default: [process_name])
  --no-build              Skips the build step
  -r, --run               Overrides the default run command
                          (default: ./[process_name])
  --run-args              Specifies arguments for the run command
  -w, --watch (multiple)  Overrides default files and appends to list of watched files
                          (default: ./src/**/*.cr, ./src/**/*.ecr)
  -h, --help              show this help
```

## Example

```bash
$ amber watch
02:58:23 Watcher    | (INFO) Watching 22 files (server reload)...
02:58:23 Watcher    | (INFO) Building project App01...
02:58:31 Watcher    | (INFO) Terminating app App01...
02:58:31 Watcher    | (INFO) Starting App01...
02:58:31 NodeJS     | (INFO) Installing dependencies...
02:58:31 NodeJS     | (INFO) Watching public directory
02:58:31 Server     | (INFO) Amber 0.7.2 serving application "App01" at http://0.0.0.0:3000
02:58:31 Server     | (INFO) Server started in development.
02:58:31 Server     | (INFO) Startup Time 00:00:00.000182000
02:58:31 Watcher    | Watching 10 client files...
```

