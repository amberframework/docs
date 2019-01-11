# System Tests

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

```ruby
module GarnetSpec
  DRIVER = :chrome
  PATH = "/usr/local/bin/chromedriver"
end
```

System tests allows test user interactions with your application, running tests in either a real chrome browser. System tests use the Selenium Standalone Server.

For creating Amber system tests, you use the spec/system directory in your application. Here's how a system test looks like:

```ruby
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

{% hint style="warning" %}
System Test currently only work with the Chrome Browser
{% endhint %}

