# Getting Started

## Quick Start

This quick start guide will help you get a full stack web application running in just a few minutes by leveraging amber’s code generators.

It will take us just 7 steps. Let’s get started!

![Let&#x2019;s get started!](https://raw.githubusercontent.com/amberframework/site-assets/master/videos/amber-quick-start.gif)

## 1. Install dependencies

{% hint style="info" %}
If you already have `crystal`, `amber`, and a database installed, you can [skip this step](quick-start.md#2-generate-a-new-amber-application).
{% endhint %}

### 1.1 Install `crystal` and `amber`

Instructions for OS X using Homebrew or MacPorts and Debian/Ubuntu are below. See full installation instructions [here](https://crystal-lang.org/docs/installation/) for RedHat & CentOS, ArchLinux & Derivatives, and more complete instructions.

#### macOS

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

#### Ubuntu or Debian

First install crystal

```text
curl https://dist.crystal-lang.org/apt/setup.sh | sudo bash
sudo apt-get install build-essential crystal
```

Then install Amber \(from source\)

```text
sudo apt-get install libreadline-dev libsqlite3-dev libpq-dev libmysqlclient-dev libssl-dev libyaml-dev libpcre3-dev libevent-dev
curl -L https://github.com/amberframework/amber/archive/stable.tar.gz | tar xz
cd amber-stable/
shards install
make install
```

You can also install Amber with [Linuxbrew](http://linuxbrew.sh/)

```bash
brew tap amberframework/amber
brew install amber
```

{% hint style="info" %}
Above are the steps for building from source, the dependencies are specific to Ubuntu/Debian. See [full installation instructions](../guides/installation.md) for other Linux Distributions.
{% endhint %}

### 1.2 Install a database

Amber works with `postgresql` \(default\), `mysql`, or `sqlite`.

If you don’t already have one of these installed, please follow the guides provided by each database maintainer:

* [Postgresql Installation Guides](https://wiki.postgresql.org/wiki/Detailed_installation_guides)
* [MySQL Installation Guides](https://dev.mysql.com/doc/refman/8.0/en/installing.html)
* [Tutorial on installing SQLite](https://www.tutorialspoint.com/sqlite/sqlite_installation.htm)

{% hint style="info" %}
On OS X any of the databases can be installed with `brew install [database]`
{% endhint %}

{% hint style="info" %}
Docker users can opt to use a database container to develop with as well. By default, a new Amber application generates a `docker-compose.yml` that can be used for this purpose. 
{% endhint %}

## 2. Generate a new Amber application

With all dependencies successfully installed, we can generate a new application with `amber new`

After the code for the new application is generated, we will `cd` into the new directory and execute a `shards install`.

The shards install command may take a little while - it has to download all shard dependencies.

{% hint style="info" %}
The default setup will use a postgresql database, use `-d mysql` or `-d sqlite` for mysql and sqlite, respectively.
{% endhint %}

```text
amber new pet-tracker
cd pet-tracker
```

## 3. Generate a resource

With the skeleton application generated, we can generate our first RESTful resource.

The `amber generate scaffold` command will help us do this.

{% hint style="info" %}
`g` is shorthand for `generate`
{% endhint %}

```text
amber g scaffold Pet name:string breed:string age:integer
```

## 4. Create and migrate the database

Generating the application and the scaffolded resource provides the configuration and migration files needed to set up the database.

`amber db` will help us do this, as you will see you can chain the `db` commands together.

```text
amber db create migrate
```

This will create a new database and run the migration to create a `pets` table with the specified columns.

## 5. Build the application and run the server

We can use `amber watch` to both build the binary application and start the server. Additionally, `amber watch` will detect code changes then recompile and restart the application automatically.

```text
amber watch
```

The `watch` commands will keep observing for file changes within the project and recompiling the application.

## 6. Use your brand new web application!

Open any browser and goto [http://localhost:3000](http://localhost:3000) You should see a home page load and “Pets” in the nav bar. If you click on the “Pets” link, you should be able to perform all seven RESTful actions for the “pets” resource.

## 7. Deploy your web application

{% page-ref page="../deployment/" %}

## List of Commands

You can use these commands to create new awesome applications :-\)

```text
amber new pet-tracker
cd pet-tracker
shards install
amber generate scaffold Pet name:string breed:string age:integer
amber db create migrate
amber watch
```

## Demo

Here is the Quick Start demo app and the source code is available on [Github](https://github.com/faustinoaq/pet-tracker).

![Quick Start Demo](https://raw.githubusercontent.com/amberframework/site-assets/master/videos/quick-start-demo.gif)

{% page-ref page="../guides/" %}
