# Exec Command

The `exec` command allows you to execute a line of code with your application code and environment loaded.

This can be helpful in executing one-liners on a remote production machine, or to quickly execute some code locally.

Here is a list of the commands available:

```
amber exec [OPTIONS] [CODE]

Arguments:
  CODE  Crystal code or .cr file to execute within the application scope
        (default: )

Options:
  -b, --back    Runs prevous command files: 'amber exec -b [times_ago]'
                (default: 0)
  -e, --editor  Prefered editor: [vim, nano, pico, etc], only used when no code or .cr file is specified
                (default: vim)
```

## Usage

In this example, an article was accidentally archived, and you want to recover it via command line:
1. Find the article and ensure it exists
   1. `amber exec 'Article.find(1)'`
1. Set the article's `archived` flag to be `false`
   1. `$ amber exec 'a = Article.find(1); (a.archived = false) if a; a.save if a;'`

```shell
# Be patient, it takes a few seconds - this is compiling your entire app
$ amber exec 'Article.find(1)'
#<Article:0x10c5f6700 @errors=[], @id=1, @title="Groundbreaking Ideas", @body="Just kidding, yet another self-important article.", @archived=true, @created_at=2017-11-14 14:13:39 UTC, @updated_at=2017-11-14 14:34:10 UTC>
# Those if statements are important, if you have a nilable type (Article | Nil)
$ amber exec 'a = Article.find(1); (a.archived = false) if a; a.save if a;'
true
# Notice the update `@archived=false`
$ amber exec 'Article.find(1)'
#<Article:0x10e640700 @errors=[], @id=1, @title="Groundbreaking Ideas", @body="Just kidding, yet another self-important article.", @archived=false, @created_at=2017-11-14 14:13:39 UTC, @updated_at=2017-11-14 14:35:06 UTC>
```

The history of your code, and the resulting output is saved in a `./tmp` directory:
```shell
$ ls -l
1510698868787_console.cr
1510698868787_console_result.log
1510698901965_console.cr
1510698901965_console_result.log
1510698912435_console.cr
1510698912435_console_result.log

# Review the output of your actions
$ cat ./tmp/*result.log
#<Article:0x10c5f6700 @errors=[], @id=1, @title="Groundbreaking Ideas", @body="Just kidding, yet another self-important article.", @archived=true, @created_at=2017-11-14 14:13:39 UTC, @updated_at=2017-11-14 14:34:10 UTC>
true
#<Article:0x10e640700 @errors=[], @id=1, @title="Groundbreaking Ideas", @body="Just kidding, yet another self-important article.", @archived=false, @created_at=2017-11-14 14:13:39 UTC, @updated_at=2017-11-14 14:35:06 UTC>
```
