# Testing

{% page-ref page="system-tests.md" %}

This guide covers built-in mechanisms in Amber for testing your application.

With this guide you will learn:

* Amber testing terminology
* How to write unit, functional, integration, and system tests for your application.

## Why write tests for your Amber Application?

* Amber makes it very easy to test your application. Amber generates skeleton test code when you generate your controllers, models.
* Tests ensure your application adheres to the specifications it was built for.
* Tests help and guide you through a code refactor.
* Amber tests can also simulate browser requests and thus you can test your application's response without having to test it through your browser.

## Amber Testing

Amber was built with testing in mind. The first time you generate an Amber application using `amber new your_app_name` a `spec` directory is generated. The contents of this directory looks as follow.

* `/spec`
  * `/controllers`
  * `/system`
  * `/models`
  * `/integrations`
  * `/mailers`

The `helpers`, `mailers`, and `models` directories are meant to hold tests for view helpers, mailers, and models, respectively. The `controllers` directory is meant to hold tests for controllers, routes, and views. The integration directory is meant to hold tests for interactions between controllers.

The `system` test directory holds system tests, which are used for full browser testing of your application. System tests allow you to test your application the way your users experience it and help you test your JavaScript as well. System tests inherit from GarnetSpec and perform in browser tests for your application.

### The Test Environment

By default every Amber application generates with three environments: `development`, `test` and `production`.

Each environment's configuration can be modified similarly. In this case, we can modify our test environment by changing the options found in `config/environments/test.yml`.

{% hint style="warning" %}
Your tests are run under AMBER\_ENV=test.
{% endhint %}

{% hint style="warning" %}
Guides for other tests like Controller tests, Integration tests and Model testing are work in progress...
{% endhint %}

