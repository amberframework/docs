# Configuring Amber

Your Amber project has a `{project_name}/config` directory, this directory contains Amber configuration files. 

### Default Environments

Your Amber project ships with three default environments: 

- Development 
- Test
- Production 

The files for each of the environment is found in `{project_name}/config/environments`.

### Getting the current environment

To get the current amber environment just type `Amber.env.to_s`. You can also check against the current environment `Amber.env== :production` or `Amber.env.development?` `Amber.env.test?` `Amber.env.production?`

### Setting the current environment

Amber sets the current environment based on the `AMBER_ENV` environment variable. The default environment is development. 

### Configuring Amber

All amber project have a directory called `{project_name}/config/` in this directory you will find

#### Initializers

You can use initializers to hold configuration settings that should be made after all of the frameworks and shards are loaded, such as options to configure settings for these parts.

- `{project_name}/config/initializers` place any initializer file here to load at runtime.

#### Environments

- `{project_name}/config/environments` all your environment YAML configuration files live here, these files get loaded based on the `AMBER_ENV` value. For example `AMBER_ENV=staging` then this will expect a `{project_name}/config/environments/staging.yml` file to exist.

#### Application.cr

- `{project_name}/config/application.cr` this is the main entry file for amber application files and it allows you to overwrite setting based on dynamic values, it makes it possible to use environment variables as your settings.

```
Amber::Server.configure do |app|
  app.name = ENV["APP_NAME"] if ENV["APP_NAME"]?
  app.host = ENV["HOSTNAME"] if ENV["HOSTNAME"]?
  app.logger = ::Logger.new(STDOUT)
  app.logger.level = ::Logger::INFO
end
```

### Where do custom settings go?

You can include new settings in any of the environment YAML files by specifying them in the secrets section.

```
database_url: postgress:://postgres:@localhost:5432/test_development
secrets: 
  custom: secret value here
```

### Encrypted Environment Settings

With Amber you can encrypt your environment setting `amber encrypt {envrionment}`, this command will open your editor to allow make changes if needed and then encrypt the file `{envrionment}.enc`.

A `{project_name}/.encryption_key` file is provided. It contains a secret_key_base that is used to decrypt your encrypted environment settings. This file is added to `.gitignore` so it will not be committed to your repository. Without the encryption key, you won't be able to decrypt your environment settings.

> It's safe then to commit the encrypted file to your repository. Never commit the encryption key!
