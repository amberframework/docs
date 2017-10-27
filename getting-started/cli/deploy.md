# Deploy Command

The deploy command lets you deploy your amber project in a heroku like fashion.

#### `amber deploy`

```shell
amber deploy [OPTIONS] [SERVER_SUFFIX]

Arguments:
  SERVER_SUFFIX  # Name of server.
                 (default: production)

Options:
  -b, --branch   # Branch to use. Default master.
                 (default: master)
  --init
  -k, --key      # API Key for service
  --no-color     # Disable colored output
  -s, --service  # Deploy to cloud service: digitalocean | heroku | aws | azure
                 (default: digitalocean)
  -t, --tag      # Tag to use. Overrides branch.
```

## Example Usage

[![Amber Deploy Cast](http://img.youtube.com/vi/EhOPNKNjdBw/0.jpg)](http://www.youtube.com/watch?v=EhOPNKNjdBw)
