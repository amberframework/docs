# Configuration

Every Amber project has a `{project_name}/config` directory. This directory contains all the configuration files used by your Amber project.

## Configuring Amber

Within the `config` directory of an Amber project, you will find several helpful configuration settings.

### Environments
Each Amber project ships with three default environments:

* Development 
* Test
* Production 

The files for each environment are found under `{project_name}/config/environments`. Each Amber environment is configured in separate YAML files under this directory. 

At runtime the framework will use the `AMBER_ENV` environment value to load the respective environment configuration into the application. For example if `AMBER_ENV=staging` the framework will expect a `config/environments/staging.yml` file to exist and attempt to load its configurations into the application.

## Getting the current environment

To get the current Amber environment you can use the `Amber.env` method. 

```ruby
Amber.env
# returns => :production
```

Alternatively, you can use built in environment helpers such as `Amber.env.development?`, `Amber.env.test?`, and `Amber.env.production?` to check the environment value.

## Setting the current environment

As mentioned previously, Amber sets the application environment based on the `AMBER_ENV` environment variable. By default, the environment is set to `development`. If you would like to set the environment to something else, you will need to configure the `AMBER_ENV` environment variable before running the server process.

For example to run Amber in the production environment, we can set the `AMBER_ENV` to `production`. 

```bash
AMBER_ENV=production amber watch
```

### Initializers

You can use initializers to load configuration settings into various application components like mailers after all of the frameworks and shards are loaded. 

To add a new initializer simply create a file and save it under `config/initializers` to load at runtime.

For example, to initialize a `Quartz` mailer:

```ruby
# config/initializers/mailer.cr

require "quartz_mailer"

Quartz.config do |c|
  c.smtp_enabled = Amber.settings.smtp.enabled

  c.smtp_address = ENV["SMTP_ADDRESS"]? || Amber.settings.smtp.host
  c.smtp_port = ENV["SMTP_PORT"]? || Amber.settings.smtp.port
  c.username = ENV["SMTP_USERNAME"]? || Amber.settings.smtp.username
  c.password = ENV["SMTP_PASSWORD"]? || Amber.settings.smtp.password

  c.use_authentication = !c.password.blank?
end
```

### Application.cr

The main entry point for an Amber application is `config/application.cr`. This application file allows you to overwrite settings using dynamic values. It also allows you to use environment variables available to the application process to configure your application.

```ruby
# config/application.cr 

Amber::Server.configure do |app|
  app.name = ENV["APP_NAME"] if ENV["APP_NAME"]?
  app.host = ENV["HOSTNAME"] if ENV["HOSTNAME"]?
  app.logger = ::Logger.new(STDOUT)
  app.logger.level = ::Logger::INFO
end
```

## Where do custom settings go?

Amber makes it easy to manage custom settings in each of the environment YAML files. You can include new settings in any of the environment YAML file by specifying them in the secrets section.

```yaml
# config/environments/development.yml

database_url: postgres://postgres:@localhost:5432/test_development
secrets: 
  custom: secret value here
```

## How do I access my custom settings?

In your application, you can access settings from your environment YAML files using the `Amber.settings.secrets` Hash

```ruby
Amber.settings.secrets["custom"] # =>  "secret value here"
```

## Encrypted Environment Settings

With Amber you can encrypt your environment setting using `amber encrypt {environment}`, this command will open your editor to allow make changes if needed and then encrypt the file `{environment}.enc`.

A `.encryption_key` file is provided. It contains a `secret_key_base` that is used to decrypt your encrypted environment settings. This file is added to `.gitignore` to avoid inclusion in version control. 

{% hint style="danger" %}
Without the encryption key, you won't be able to decrypt your environment settings.
{% endhint %}

{% hint style="danger" %}
**Never commit the encryption key!**

Setup your production machine and use the variable `AMBER_ENCRYPTION_KEY`

See more on [encrypt section](../cli/encrypt.md)
{% endhint %}
