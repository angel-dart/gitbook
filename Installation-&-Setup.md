# Getting Started
Let's get it started, ha!

## Installation

Firstly, ensure you have the [Dart SDK](https://www.dartlang.org/downloads/) installed.

Now, install the [Angel CLI](https://github.com/angel-dart/cli). The CLI includes several code generators and commands that will help you expedite your development cycle.

```bash
$ pub global activate angel_cli
```

Now, let's create a sample project, called `hello`.

Run:

```bash
$ angel init hello
```

This will create a folder called `hello`, and copy the [Angel boilerplate](https://github.com/angel-dart/angel) into it. If you wanted to initialize a project within the current directory, instead of making new one, you could have run:

```bash
$ angel init
```

You'll notice that the following folder structure is there for you:

```
.idea/ - IntelliJ metadata.
.vscode/ - VSCode metadata.
bin/ - Contains a script to run the application.
config/ - Static configuration files.
lib/
  src/
    config/ - Attach miscellaneous plugins to your application.
      plugins/ - Plugins you have written yourself.
    models/ - In-code representations of the data your application manages.
    routes/ - Routing config
      controllers/ - Contains your controllers.
    services/ - Contains RESTful services.
    validators/ - Contains validators that can validate input on the client and server sides.
test/ - Test files for services and endpoints.
tool/ - Helper files for build/task tools, such as Grinder.
views/ - Mustache views.
web/ - Client-side code.
```

It's easy to run our server. Just type the following:

```bash
$ angel start
```

And there you have it - you've created an Angel application!

Next up: [Requests and Responses](https://github.com/angel-dart/angel/wiki/Requests-&-Responses)