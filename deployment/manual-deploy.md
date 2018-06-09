# Manual Deploy

## In your development machine:

1. Open your production env file with `amber encrypt` then edit the database URL, and disable or enable logs. Also see [Encrypt](../cli/encrypt.md).
2. If you have assets and you're using NPM, use `npm install . && npm run release` to compile/minify `.css` and `.js` files.

## In your production machine:

1. Install`crystal` and `shards` commands \([See installation guide](https://crystal-lang.org/docs/installation/)\). You can compile `bin/amber` tool using `shards build amber` in your production project to migrate your database.
2. Then copy your project repository and ensure to setup `AMBER_ENCRYPTION_KEY` and `AMBER_ENV=production` in your environments variables. You can get your encryption key from `.encryption_key` file in your development machine. Then inside your project's folder, install your shards dependencies with `shards install --production` and compile your executable with `shards build <your-app> --release`.
3. Finally run your project executable with `bin/<your-app>` .

{% hint style="info" %}
Also you can try to [cross-compile](https://crystal-lang.org/docs/syntax_and_semantics/cross-compilation.html) to avoid install these dependencies but this feature is a bit buggy on most OS.

[Alpine Linux](https://store.docker.com/community/images/jrei/crystal-alpine) is great to use `crystal build --static` flag, though.
{% endhint %}

{% hint style="warning" %}
Compilation may take a while because `--release` enables compiler optimizations. You can avoid `--release` if you server is low end \(less 256 RAM\)

Amber performance is still acceptable on non-release mode
{% endhint %}

{% hint style="danger" %}
You may need sudo permission if you're using port 80 or ports &lt; 1000
{% endhint %}

You can use `iptables` to redirect port 80 to 8080 or whatever port are you using in production.

```text
iptables -t nat -A PREROUTING -p tcp --dport 80 -j REDIRECT --to-port 8080
```

Optionally you can setup a system service to monitor your app very easy, and configure a `.git/hook/post-receive` to deploy like Heroku using `git push production` . Amber Team is already using a [systemd service ](https://github.com/amberframework/amberframework.org#systemd-service)to manage [Amber Framework Website](https://amberframework.org/) and the`post-receive` hook below to manage deployment using git.

```bash
#!/bin/bash
set -e

export PORT=80
export AMBER_ENV=production

if [ "$GIT_DIR" = "." ]; then
  # The script has been called as a hook; chdir to the working copy
  cd ..
  unset GIT_DIR
fi

# try to obtain the usual system PATH
if [ -f /etc/profile ]; then
  PATH=$(source /etc/profile; echo $PATH)
  export PATH
fi

# get the current branch
head="$(git symbolic-ref HEAD)"

# read the STDIN to detect if this push changed the current branch
while read oldrev newrev refname
do
  [ "$refname" = "$head" ] && break
done

# abort if there's no update, or in case the branch is deleted
if [ -z "${newrev//0}" ]; then
  exit
fi

# check out the latest code into the working copy
umask 002
git reset --hard

logfile=log/deploy.log

if [ -z "${oldrev//0}" ]; then
  # this is the first push; this branch was just created
  mkdir -p log tmp bin
  chmod 0775 log tmp bin
  touch $logfile
  chmod 0664 $logfile

  # init submodules
  git submodule update --recursive --init 2>&1 | tee -a $logfile
else
  # log timestamp
  echo ==== $(date) ==== >> $logfile

  ################################################################################
  ####  BEGIN: Amberframework specific Code                                  #####
  ################################################################################

  shards install

  if [ -f bin/amber ]; then
    echo "amber already installed"
  else
    crystal build lib/amber/src/amber/cli.cr -o bin/amber --stats
  fi

  echo "Migrating..."
  ./bin/amber db create migrate || true
  echo "Building application in release mode"
  crystal build src/amberframework.cr -o bin/amberframework --release --stats
  systemctl status amberframework
  sudo systemctl restart amberframework
  systemctl status amberframework
fi
```

Then add your production repository to your development machine using `git remote add`

```text
git remote add production <user>@<ip>:/home/<user>/<repository>
```

* `<user>` is a valid user with your public ssh key inside `~/.ssh/authorized_keys`
* `<ip>` is your production machine IP \(you can use a domain as well\).
* `<repository>` is your project with your`.git` folder inside.

