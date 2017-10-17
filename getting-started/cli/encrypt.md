# Encrypt Command

The `encrypt` command allows you to encrypt and edit your environment settings. 
This allows you to store production secrets in your repo safely.

Here is a list of the commands available:

```
amber encrypt [OPTIONS] [ENV]

Arguments:
  ENV  Environment settings to encrypt (default: production)

Options:
  -e, --editor  Prefered Editor: [vim, nano, pico, etc]
                (default: vim)
  --noedit      Skip editing and just encrypt
```

## Editing Encrypted Files

`amber encrypt production` will use the secret key in `.amber_secret_key` or `ENV[AMBER_SECRET_KEY]` to decrypt `config/environments/.production.enc` and open it in your favorite editor. 

When you save and exit it will encrypt again.

![amber encrypt demo](https://raw.githubusercontent.com/amberframework/online-docs/master/assets/amber_encrypt.gif "Amber Encrypt Demo")


## Encrypt unencrypted environment settings
 
`amber encrypt development` will encrypt `config/environments/development.yml` using same keys as above. 

Editor will be opened if file is already encrypted.

`amber encrypt development --noedit` will encrypt file if unencrypted but do nothing if it's already encrypted. Editor will not be opened.

Production is encrypted by default.
