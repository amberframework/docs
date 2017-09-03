# Installing Amber

The **Amber CLI** offers you a set of command line tools to **create**, **generate**, **scaffold** and **run** Amber.

### Installing the Amber CLI

#### Method 1 - From Source:

Install crystal by following these instructions. [https://crystal-lang.org/docs/installation/](https://crystal-lang.org/docs/installation/ "Install Crystal")

Next download and install `amber`

```bash
git clone https://github.com/amber-crystal/amber.git
cd amber/
shards install
make
```

#### Method 2 - Use Homebrew \(OSX Only\)

```bash
brew install amber-crystal/amber/amber
```

Optionally you can install as  follow:

```bash
brew tap amber-crystal/amber
brew install amber-crystal/amber/amber
```

### Usage

You should now be able to run`amber`in the command line.  
Now that you successfully have install amber-cmd, you can query to see a list of available commands

    $ amber --help
    amber [OPTIONS] SUBCOMMAND

    Amber::CMD

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

    Options:
      -t, --template [name]           # Preconfigure for selected template engine. Options: slang | ecr 
      -d, --database [name]           # Preconfigure for selected database. Options: pg | mysql | sqlite
      -h, --help                      # Describe available commands and usages
      -v, --version                   # Prints Amber version
      --deps                          # Installs project dependencies

    Example:
      amber new ~/Code/Projects/weblog
      This generates a skeletal Amber installation in ~/Code/Projects/weblog.





