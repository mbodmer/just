### User `justfile`s

If you want some recipes to be available everywhere, you have a few options.

First, create a `justfile` in `~/.user.justfile` with some recipes.

#### Recipe Aliases

If you want to call the recipes in `~/.user.justfile` by name, and don’t mind creating an alias for every recipe, add the following to your shell’s initialization script:

````sh
for recipe in `just --justfile ~/.user.justfile --summary`; do
  alias $recipe="just --justfile ~/.user.justfile --working-directory . $recipe"
done
````

Now, if you have a recipe called `foo` in `~/.user.justfile`, you can just type `foo` at the command line to run it.

It took me way too long to realize that you could create recipe aliases like this. Notwithstanding my tardiness, I am very pleased to bring you this major advance in `justfile` technology.

#### Forwarding Alias

If you’d rather not create aliases for every recipe, you can create a single alias:

````sh
alias .j='just --justfile ~/.user.justfile --working-directory .'
````

Now, if you have a recipe called `foo` in `~/.user.justfile`, you can just type `.j foo` at the command line to run it.

I’m pretty sure that nobody actually uses this feature, but it’s there.

¯\\\_(ツ)\_/¯

#### Customization

You can customize the above aliases with additional options. For example, if you’d prefer to have the recipes in your `justfile` run in your home directory, instead of the current directory:

````sh
alias .j='just --justfile ~/.user.justfile --working-directory ~'
````