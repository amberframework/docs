## Amber CLI Commands

```shell
$ amber --help
amber [OPTIONS] SUBCOMMAND

Amber

The `amber new` command creates a new Amber application with a default
directory structure and configuration at the path you specify.

You can specify extra command-line arguments to be used every time
`amber new` runs in the .amber.yml configuration file in your project
root directory

Note that the arguments specified in the .amber.yml file does not affect the
defaults values shown above in this help message.

Usage:
amber new [app_name] -d [pg | mysql | sqlite] -t [slang | ecr] --deps

Commands:
  amber c console                 # Starts a amber console   
  amber g generate [SUBCOMMAND]   # Generate Amber classes
  amber n new                     # Generate a new amber project
  amber m migrate [SUBCOMMAND]    # Performs database migrations tasks
  amber w watch                   # Starts amber server and rebuilds on file changes
  amber routes                    # Prints the routes (In Development)
  amber r run [OPTION]            # Compiles and runs your project. Options: [-p --port | -e -environment]
  amber deploy [OPTION]          # Provisions server and deploys project. [-s --service | -k --key | -t --tag | -b --branch]
  amber encrypt [OPTION]          # Encrypts environment YAML file. [env | -e --editor | --noedit]

Options:
  -t, --template [name]           # Preconfigure for selected template engine. Options: slang | ecr
  -d, --database [name]           # Preconfigure for selected database. Options: pg | mysql | sqlite
  -h, --help                      # Describe available commands and usages
  -v, --version                   # Prints Amber version
  --deps                          # Installs project dependencies

Example:
  amber new ~/Code/Projects/weblog
  This generates a skeletal Amber installation in ~/Code/Projects/weblog.
```

## Usage

```sh
amber new [your_app] -d [pg | mysql | sqlite] -t [slang | ecr] --deps
cd [your_app]
```
options: `-d` defaults to pg. `-t` defaults to slang. `--deps` will run `crystal deps` for you.
