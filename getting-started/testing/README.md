# Testing

Testing is an essential part to the software development cycle. As the software that we write gets bigger 
and new features gets added our softwares grows in complexity and becomes more restricted to understand the intrarelations 
of its components. Test gives us a level of confidence in our work, correctness and saves us time and money.

With automated tests we catch bugs sooner, automated tests also offer a quicker feedback loop to programmers, as they 
don’t have to walk through every path in their application by manually.

> “As software developers, we are hired to write code that works. If our code doesn’t work, we have failed.”
> Excerpt From: Josh Steiner. “Testing Rails.” 

Amber provides a set of files to easily write tests for its major components. As you generate files with the `Amber CLI` 
it will try to generate tests when ppossible.

# Spec Directory

The `./spec` directory is where all your tests lives. 

```shell
spec
├── system
│   ├── creating_posts_spec.cr
├── controllers
│   ├── post_controller_spec.cr
├── integrations
│   ├── post_controller_spec.cr
├── models
│   ├── post_spec.cr
└── amber_spec.cr
```

