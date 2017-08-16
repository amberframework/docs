# Amber Application Configuration

```ruby
Amber::Server.instance.config do |app|
  # Server options
  app_path = __FILE__ # Do not change unless you understand what you are doing.
  app.name = "Ambercr.io web application."
  port = ENV["PORT"] || 8080
  app.port = port.to_i
  app.env = ENV.fetch("AMBER_ENV", "development") .colorize(:green).to_s
  app.log = ::Logger.new(STDOUT)
  app.log.level = ::Logger::INFO
  app.host = "0.0.0.0"
  app.port_reuse = true
  app.session = {
    :key     => "name.session",
    # store can be [:signed_cookie, :encrypted_cookie, :redis]
    :store   => :signed_cookie,
    # 0, will make the session last as long as the browser is open, upon closing, session will be terminated
    :expires => 120,
    :secret  => "secret",
    # when store equals to :redis, must specify redis url
    :redis_url => "redis://localhost:6379",
  }
end
```



