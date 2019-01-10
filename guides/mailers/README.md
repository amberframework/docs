# Mailers

Mailers are implemented using the [Quartz-Mailer](https://github.com/amberframework/quartz-mailer) library. This library is not required by default so you must require it in a config file.

```ruby
# config/mailer.cr

require "quartz_mailer"

### If you use a base mailer class you must require it before the others
#require "../src/mailers/base_mailer" 

require "../src/mailers/**"
```

## Define a Mailer

The mailer has the ability to set the `from`, `to`, `cc`, `bcc`, and `subject` as well as both `text` and `html` body formats. You may use the `render` helper to create the body of the email.

```ruby
class WelcomeMailer < Quartz::Composer
  def sender
    address email: "info@amberframework.org", name: "Amber"
  end

  def initialize(name : String, email : String)
    to email: email, name: name # Can be called multiple times to add more recipients

    subject "Welcome to Amber"

    text render("mailers/welcome_mailer.text.ecr")
    html render("mailers/welcome_mailer.html.slang", "mailer-layout.html.slang")
  end
end
```

## Deliver an Email

```ruby
WelcomeMailer.new(name, email).deliver
```
