---
description: That feeling you get when that right tool did all the work
---

# Installation

Amber CLI offers you a set of command line tools to **create**, **generate**, **scaffold** and **run** your projects easily and fast.

## Linux

Ensure you have the necessary dependencies:

* **Git:** Use your platform specific package manager to install `git`
* **Crystal:** Follow the instructions to get `crystal` on this page: [Crystal Installation](https://crystal-lang.org/install/)
* **NodeJS and Webpack:** `node` is an optional dependency and is used to compile JavaScript and other assets.
* **PostgreSQL:** `postgres` is a relational database server. Amber configures applications to use this database adapter by default, but you can switch to MySQL by passing the flag `--database mysql` when creating a new application.

Once you have these dependencies, You can build the `amber` tool from source:

### From Source

Download and install `amber`

```bash
curl -L https://github.com/amberframework/amber/archive/stable.tar.gz | tar xz
cd amber-stable/
make
sudo make install
```

If you run into an issue on compiling regarding `Unhandled exception in spawn: fork: Cannot allocate memory` it means you don't have enough memory. This can easily be solved by adding a swapfile.

```bash
sudo dd if=/dev/zero of=/swapfile bs=2k count=1024k
sudo mkswap /swapfile
sudo chmod 600 /swapfile
sudo swapon /swapfile
```

### For Debian & Ubuntu

* These are necessary to compile the CLI:
* `sudo apt-get install build-essential libreadline-dev libsqlite3-dev libpq-dev libmysqlclient-dev libssl-dev libyaml-dev`

### For RedHat & CentOS

* `sudo yum groupinstall development tools`
* `sudo yum install readline-devel sqlite-devel openssl-devel libyaml-devel gc-devel libevent-devel`

### For ArchLinux & derivates

* Install the CLI from [AUR package](https://aur.archlinux.org/packages/amber/). Dependencies are automatically installed.
* `yaourt -S amber`

You should now be able to run `amber` in the command line.

## macOS

Installing Amber with these package managers also installs Crystal.

- Homebrew

  ```text
  brew tap amberframework/amber
  brew install amber
  ```

- MacPorts

  ```text
  sudo port selfupdate
  sudo port install amber
  ```

### From source

Known problems while trying to build Amber from source on macOS:

- If you see linker problems, such as `ld: library not found for -lssl (this usually means you need to install the development package for libssl)`, you
  may need to set some (or all) of those environment variables:

  ```text
  export PKG_CONFIG_PATH="/usr/local/opt/openssl/lib/pkgconfig"
  export LDFLAGS="-L/usr/local/opt/openssl/lib"
  export CPPFLAGS="-I/usr/local/opt/openssl/include"
  ```
