# Installing Amber

The **Amber CLI** offers you a set of command line tools to **create**, **generate**, **scaffold** and **run** Amber.

### Installing the Amber CLI

#### Linux

Ensure you have the necessary dependencies:

- `git`: Use your platform specific package manager to install `git`
- `crystal`: Follow the instructions to get `crystal` on this page: [Crystal Installation](https://crystal-lang.org/docs/installation/index.html)

##### From Source

Once you have these dependencies, You can build the `amber` tool from source:

Download and install `amber`

```
$ git clone git@github.com:amberframework/amber.git
$ cd amber/
$ shards install
$ make install
```

##### For Debian & Ubuntu
- These are necessary to compile the CLI:
- `sudo apt-get install build-essential libreadline-dev libsqlite3-dev libpq-dev libmysqlclient-dev libssl-dev`

##### For RedHat & CentOS
- `sudo yum groupinstall development tools`
- `sudo yum install readline-devel sqlite-devel openssl-devel libyaml-devel gc-devel libevent-devel`

##### For ArchLinux & derivates
- Install the CLI from [AUR package](https://aur.archlinux.org/packages/amber/). Dependencies are automatically installed.
- `yaourt -S amber`

You should now be able to run `amber` in the command line.

#### Homebrew MacOS 

```
brew install amber-crystal/amber/amber
```

Optionally you can install as  follow:

```
brew tap amber-crystal/amber
brew install amber-crystal/amber/amber
```




