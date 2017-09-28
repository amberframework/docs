# Console Command

The console command lets you interact with your Amber application from the command line.

It pre-loads your project files making them available to interact with object right from the command line.

#### `amber console` or `amber c`

```shell
amber console [OPTIONS]

Options:
  -d, --debug  # Runs console in debug mode enabling verbose output.
```

## Example Usage

```shell
± amber c
 => ok
icr(0.23.1) > 2 + 2
 => 4
icr(0.23.1) > Amber::VERSION
 => "0.2.6"
icr(0.23.1) > exit
```
