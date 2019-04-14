# SSL

For development use self-signed keys and edit ```config/environments/development.yml```

```
ssl_key_file: example.key
ssl_cert_file: example.crt
```

For production, please use CertBot (https://certbot.eff.org) and edit ```config/environments/production.yml```

It is possible to set this in your Amber configure block as well, in ```config/application.cr```

```
Amber::Server.configure do |setting|
  # Server options
  setting.name = "Example web application."
  setting.port = 3000 # Port you wish your app to run
  setting.host = "0.0.0.0"
  setting.ssl_key_file = "example.key"
  setting.ssl_cert_file = "example.crt"
end
```
