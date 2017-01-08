# autorun

Manage program processes based on file changes.

This simple program makes it easy to have programs run automatically
as you are writing them; the practical use-cases are many more.

E.g. `autorun foo.js node : -test` spawns a process running `node` with the
arguments `foo.js`, `-test`. Whenever `foo.js` changes, any running process is
first terminated and then a new one is spawned. If the file's name changed,
the newly spawned process'es arguments will contain the new filename.

```
$ autorun -h
Automatically spawn and manage processes as file changes.
Usage: autorun [options] <file> [<cmdpattern>]
options:
  -h[elp]        Show description and detailed help for this program.
  -v[erbose]     Print some details to stdout.
  -s[top]        Exit with an error if <file> disappears from the file system.
  -clear-screen  Clear terminal screen before (re)starting the command.
  -no-banner     Don't print banner to stdout when restarting the command.

<file>        File to watch for changes

<cmdpattern>  Pattern for command to execute, replacing ":" with <file>.
              If not specified or if <cmdpattern> does not include a ":"
              argument, <file> is executed with <cmdpattern> as arguments.
              If you need to pass an argument of value ":", use "\" which
              will be expanded to ":" rather than <file>.

Examples: (INVOCATION  # COMMAND EXECUTED)
  autorun foo.sh bar             # bar
  autorun foo.sh : bar           # foo.sh bar
  autorun foo.js node --check :  # node --check foo.js
  autorun foo.py python : \\: a  # python foo.py : a
```
