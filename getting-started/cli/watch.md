# Watch Command

The `amber watch` compiles and runs your project. It also monitors for changes and recompiles when necessary.

Here is a list of the commands available:

`amber watch` or `amber w`

```sh
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

```sh
~/crystal/amberdemo  ᐅ amber w                
🤖  Your SentryBot is vigilant. beep-boop...
🤖  watching file: ./src/controllers/application_controller.cr
🤖  watching file: ./src/controllers/home_controller.cr
🤖  watching file: ./src/controllers/pet_controller.cr
🤖  watching file: ./src/controllers/static_controller.cr
🤖  watching file: ./src/dbtest.cr
🤖  watching file: ./src/models/pet.cr
🤖  watching file: ./config/application.cr
🤖  watching file: ./config/routes.cr
🤖  watching file: ./src/views/home/index.slang
🤖  watching file: ./src/views/layouts/_nav.slang
🤖  watching file: ./src/views/layouts/application.slang
🤖  watching file: ./src/views/pet/_form.slang
🤖  watching file: ./src/views/pet/edit.slang
🤖  watching file: ./src/views/pet/index.slang
🤖  watching file: ./src/views/pet/new.slang
🤖  watching file: ./src/views/pet/show.slang
🤖  compiling dbtest...
🤖  starting dbtest...
I, [2017-10-26 21:40:50 -0600 #30657]  INFO -- : [Amber 0.1.8] serving application "Amberdemo web application." at http://0.0.0.0:3000
I, [2017-10-26 21:40:50 -0600 #30657]  INFO -- : Server started in development.
I, [2017-10-26 21:40:50 -0600 #30657]  INFO -- : Startup Time 00:00:00.0012720
```
