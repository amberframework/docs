# A Guide to Testing Amber Applications

This guide covers built-in mechanisms in Amber for testing your application.

With this guide you will learn:

* Amber testing terminology
* How to write unit, functional, integration, and system tests for your application.


## Why write tests for your Amber Application?

* Amber makes it very easy to test your application. Amber generates skeleton test code when you generate your controllers, models.
* Tests ensure your application adheres to the specifications it was built for.
* Tests help and guide you through a code refactor.
* Amber tests can also simulate browser requests and thus you can test your application's response without having to test it through your browser.

### Amber Testing

Amber was built with testing in mind. The first time you generate an Amber application using `amber new your_app_name` a `spec` directory is generated. The contents of this directory looks as follow.

- /spec
	- /controllers
	- /system
	- /models
	- /integrations
	- /mailers

The `helpers`, `mailers`, and `models` directories are meant to hold tests for view helpers, mailers, and models, respectively. The `controllers` directory is meant to hold tests for controllers, routes, and views. The integration directory is meant to hold tests for interactions between controllers.

The `system` test directory holds system tests, which are used for full browser testing of your application. System tests allow you to test your application the way your users experience it and help you test your JavaScript as well. System tests inherit from GarnetSpec and perform in browser tests for your application.

### The Test Environment

By default every Amber application generates with three environments: `development`, `test` and `production`.

Each environment's configuration can be modified similarly. In this case, we can modify our test environment by changing the options found in `config/environments/test.yml`.

> Your tests are run under AMBER_ENV=test.

### System Specs

**Setting Up System Specs**

We have made it as simple as possible to have your system specs. Before running your specs ensure you have installed the `chromedriver` and that `selenium-server standalone` is in your system path.

**Mac OS**

You can install the chromedriver and selenium standalone server with `brew`

```bash
brew install selenium-server-standalone
brew install chromedriver
```

This will install the chrome driver on the system path `/usr/local/bin/chromedriver`

If you're running in a different OS such as Linux you can specify the chromedriver path as such

```crystal
module GarnetSpec
  DRIVER = :chrome
  PATH = "/usr/local/bin/chromedriver"
end
```

System tests allows test user interactions with your application, running tests in either a real  chrome browser. System tests use the Selenium Standalone Server.

For creating Amber system tests, you use the spec/system directory in your application. Amber provides a generator to create a system test skeleton for you.

```crystal
amber g system_test users
```

Here's what a freshly-generated system test looks like:

```crystal
class SomeFeature < GarnetSpec::System::Test
  scenario "user visits amber framework and sees getting started button" do
    visit "http://www.amberframework.org"
    timeout 1000
    click_on(:css, "header a.btn.btn-primary")
    wait 2000
    element(:tag_name, "body").text.should contain "Introduction"
  end

  scenario "user visits amberframwork homepage and sees logo" do
    visit "http://www.amberframework.org"
    wait 2000
    element(:class_name, "img-amber-logo").attribute("src").should match(
      %r(https://www.amberframework.org/assets/img/amber-logo-t-bg.png)
    )
  end
end
```

Run your specs with `crystal spec`

> Important: System Test currently only work with the Chrome Browser


