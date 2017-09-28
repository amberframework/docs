# Amber Application Configuration

While conventions remove the need to configure all of Amber, you’ll still need to configure a few things like your database credentials. Additionally, there are optional configuration options that allow you to swap out default values & implementations with ones tailored to your application.

## Configuring your Application

Configuration is generally stored in YAML files, and loaded during the application bootstrap. Amber comes with three configuration files by default, one for each environment Production, Test and Development.

## Environments 

#### Development 
TODO
#### Test
TODO
#### Production
TODO


#### Example config/application.cr
Additionallly you can overwrite your configuration yamls by setting the 

```crystal
Amber::Server.configure do |server|
  server.name         = "Ambercr.io web application."
  server.port         = (ENV["PORT"] || 8080).to_i
  server.env          = ENV.fetch("AMBER_ENV", "development") .colorize(:green).to_s
  server.log          = ::Logger.new(STDOUT)
  server.log.level    = ::Logger::INFO
  server.host         = "0.0.0.0"
  server.port_reuse   = true
  server.ssl_key_file = ""
  server.ssl_cert_file= ""
  server.session = {
    :key     => "name.session",
    :store   => :signed_cookie,
    :expires => 120,
    :secret  => "secret",
    :redis_url => "redis://localhost:6379",
  }
  server.secrets = {
  }
end
```

## Database Configuration
TODO

## Session Confiiguration
TODO

## Websockets Configuration
TODO

