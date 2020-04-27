# Plugins

## Plugins

Amber plugins are intended to extend the capabilities of Amber and make it easy for developers to create powerful, feature rich Amber applications by adding in plugin features.

### Plugin contributors

Plugins are provided by independent contributors in their own GitHub accounts and also in the [amberplugin](https://github.com/amberplugin) Github account.  To use a plugin you specify the GitHub account name and plugin name in the format _githubaccount/plugin_name_.  However plugins installed from the **amberplugin** github account can be specified by their repository name only.
Plugin creators should add the **amber-plugin** topic to the GitHub repository for their plugin.

### Available plugins

See the [Amber Plugins](https://github.com/topics/amber-plugin) that are available on GitHub for a list
of plugins that have been created by plugin contributors. Visit the plugin repository for more information about each plugin.

### Amber plugin install

To add a plugin to your application you run the command
```
$ amber plugin add <plugin_spec>
```

Where plugin_spec can be one of

- a plugin in the **amberplugin** github organisation
- a plugin in another github account
- a URL to a zip file containing the plugin templates 


### Example CLI Usage

Using `amber plugin add amberplugin/authorize` will add the __authorize__ plugin to your application from the plugin template at https://github.com/amberplugin/authorize.

Using `amber plugin add authorize` will also add the __authorize__ plugin to your application from the plugin template at https://github.com/amberplugin/authorize.  When the plugin spec does not contain a forward slash the name is assumed to be the name of a plugin repository in the https://github.com/amberplugin organisation account.

Using `amber plugin add damianham/authorize` will add the __authorize__ plugin to your application from the plugin template at https://github.com/damianham/authorize.  Note that no such plugin exists at that URL - this is for illustration purposes only.

### Plugin setup

It is important to read the plugin README in the plugin repo (and any other documentation referred to by the plugin) to ensure you follow all the steps required to setup the plugin in your application.  The setup for a plugin may include 

- Configuration
- Hook overrides
- Database migration
- Modifying existing classes
- Modifying email templates

#### Configuration

A plugin may include a configuration file.  You will find the configuration files for plugins in __{ROOT}/src/plugins/initializers__. 

#### Hooks

A plugin may provide hooks for before and after event processing.  You will find the hook override classes in __{ROOT}/src/plugins/hooks__.  The hook override class files may not have any methods defined in them but they will refer to the location of the hook class file that contains the empty hook methods so you can see what hook methods are available.  The plugin may also document the hook override methods in the plugin documentation.

#### Database migrations

A plugin may provide one or more database migrations.  A plugin should namespace any tables that are created, e.g. **plugin_blog_posts** instead of **blog_posts** to ensure there are no table name clashes.  After adding the plugin you may need to run
```
$ amber db migrate
```

#### Modifying existing classes

A plugin may be designed to add methods to existing classes.  For example a plugin may add columns and methods to the users table and User class.  The plugin documentation should include information about what steps need to be taken to include the plugin features into existing classes.

#### Modifying email templates

A plugin may provide features to send email to users or send email to users as part of the plugin operations.  Therefore the plugin will include email templates that you can modify to suit your application.  The email templates should be located in __{ROOT}/plugins/{plugin_name}/mailers__, e,g, __{ROOT}/plugins/authorize/mailers__

####  Integrating the plugin

To integrate the plugin into your web application you may need to provide links in your application menu to route the application user to the features provided by the plugin.  For example the **amberplugin/authorize** plugin has a feature to invite new users to your application.  In order to integrate this feature into your application you would add a button or link in the web application so that when a logged in user clicked on the button or link they would be taken to **/authorize/invite**.  The application user would see a web form asking for a person's name and email address.  When the form is submitted an invitation is sent by email to the email address given in the web form.

### Custom Plugin

When a plugin doesn't quite suite your requirements, it may be easy to modify. Plugins contain crystal code files but may also include either **.ecr** or **.slang** view files.  The plugin installer CLI command uses the [Liquid](https://github.com/TechMagister/liquid.cr) template engine to process the plugin template files.  These files can have either a **.lqd** or **.liquid** extension.  For plugin developers it is important to remember that when rendering a view from a controller action within a plugin you need to use 
```
    render_module("index.slang")

    # rather than render("index.slang")
```

Modified plugins can be stored and sourced locally or made available to other Amber users from your own GitHub account.

Download a plugin that you want to modify, extract it, and use the extracted plugin on the command line. For example:

1. `git clone https://github.com/amberplugin/authorize.git ~/myauthorize`
2. `# modify the plugin in ~/myauthorize in some way`
3. `cd ~/myamber_webapp`
3. `amber plugin add ~/myauthorize`  # install the plugin from a local folder
4. `configure the plugin initializers and hooks`
5. `integrate the plugin routes into your Amber application`
6. `amber db migrate`


Now open a web browser at [http://localhost:3000](http://localhost:3000) and visit one of the plugin routes.

### Contributing your plugin

The best way to do this is to fork an existing plugin and then modify it.  Visit the GitHub repository of a plugin you want to use as a starting point, e.g. one of the plugins listed above in the **Available plugins** section, and create your own fork.  After you have modified the plugin and pushed your changes you can install the plugin over the net with

```
amber plugin add your_github_name/your_plugin_name
```

You can easily contribute your plugin to the Amber community.  By adding the **amber-plugin** topic to your plugin repository on GitHub, it will be added to the list of [Amber Plugins](https://github.com/topics/amber-plugin).
