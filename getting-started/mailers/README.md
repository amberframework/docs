# Mailers

The mailer has the ability to set the`from`,`to`,`cc`,`bcc`,`subject`and`body`. You may use the`render`helper to create the body of the email.

```crystal
class WelcomeMailer < Quartz::Mailer
  def initialize
    super
    from "Amber Crystal", "info@ambercr.io"
  end

  def deliver(name: String, email: String)
    to name: name, email: email
    subject "Welcome to Amber"
    body render("mailers/welcome_mailer.slang", "mailer.slang")
    super()
  end
end
```

To delivery a new email:

```crystal
mailer = WelcomeMailer.new
mailer.deliver(name, email)
```



