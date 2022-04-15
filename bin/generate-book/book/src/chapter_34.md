### Recipe Parameters

Recipes may have parameters. Here recipe `build` has a parameter called `target`:

````make
build target:
  @echo 'Building {{target}}…'
  cd {{target}} && make
````

To pass arguments on the command line, put them after the recipe name:

````sh
$ just build my-awesome-project
Building my-awesome-project…
cd my-awesome-project && make
````

To pass arguments to a dependency, put the dependency in parentheses along with the arguments:

````make
default: (build "main")

build target:
  @echo 'Building {{target}}…'
  cd {{target}} && make
````

Parameters may have default values:

````make
default := 'all'

test target tests=default:
  @echo 'Testing {{target}}:{{tests}}…'
  ./test --tests {{tests}} {{target}}
````

Parameters with default values may be omitted:

````sh
$ just test server
Testing server:all…
./test --tests all server
````

Or supplied:

````sh
$ just test server unit
Testing server:unit…
./test --tests unit server
````

Default values may be arbitrary expressions, but concatenations must be parenthesized:

````make
arch := "wasm"

test triple=(arch + "-unknown-unknown"):
  ./test {{triple}}
````

The last parameter of a recipe may be variadic, indicated with either a `+` or a `*` before the argument name:

````make
backup +FILES:
  scp {{FILES}} me@server.com:
````

Variadic parameters prefixed with `+` accept *one or more* arguments and expand to a string containing those arguments separated by spaces:

````sh
$ just backup FAQ.md GRAMMAR.md
scp FAQ.md GRAMMAR.md me@server.com:
FAQ.md                  100% 1831     1.8KB/s   00:00
GRAMMAR.md              100% 1666     1.6KB/s   00:00
````

Variadic parameters prefixed with `*` accept *zero or more* arguments and expand to a string containing those arguments separated by spaces, or an empty string if no arguments are present:

````make
commit MESSAGE *FLAGS:
  git commit {{FLAGS}} -m "{{MESSAGE}}"
````

Variadic parameters can be assigned default values. These are overridden by arguments passed on the command line:

````make
test +FLAGS='-q':
  cargo test {{FLAGS}}
````

`{{…}}` substitutions may need to be quoted if they contain spaces. For example, if you have the following recipe:

````make
search QUERY:
  lynx https://www.google.com/?q={{QUERY}}
````

And you type:

````sh
$ just search "cat toupee"
````

`just` will run the command `lynx https://www.google.com/?q=cat toupee`, which will get parsed by `sh` as `lynx`, `https://www.google.com/?q=cat`, and `toupee`, and not the intended `lynx` and `https://www.google.com/?q=cat toupee`.

You can fix this by adding quotes:

````make
search QUERY:
  lynx 'https://www.google.com/?q={{QUERY}}'
````

Parameters prefixed with a `$` will be exported as environment variables:

````make
foo $bar:
  echo $bar
````