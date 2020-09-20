# rsms-utils

Collection of command-line programs to help with everyday computer life.

- [color-space](#color-space) — Inspect and conversion color spaces & profiles in image files.
- [autorun](#autorun) — Manage program processes based on file changes

**Installation:** The idea is to add these tools to your `PATH`. Something like this:

```
cd ~/src
git clone https://github.com/rsms/rsms-utils.git
# edit your ~/.bashrc or ~/.zshrc shell file, adding:
export PATH=$PATH:$HOME/src/rsms-utils
```


## color-space

```
$ color-space -h
Inspect and convert color spaces & profiles in image files.
Usage: color-space <command> ...

color-space help
  Show help message and exit

color-space info <file>... [-sha256]
  Display color space & profile info.
  If -sha256 is specified, the output changes to a tab-separated table:
    filename<TAB>checksum<TAB>profilename
  The checksum is computed from the ICC profile data in the files.
  This can be compared with the checksum of a ICC file, e.g:
    shasum -b -a 256 p3.icc

color-space assign <profile> <file>... [-output <file>]
  Assign profile; reinterpret the color values in the image as being defined
  in the specified profile's color space.

color-space convert <profile> <file>... [-output <file>]
  Convert to color space and assign profile

color-space strip <file>... [-output <file>]
  Remove any color profile(s). This may lead to unexpected results if the
  implicit color space for the image format is different than the color space
  that the image data is defined in. For example, a PNG without an ICC profile
  is by the PNG standard implicitly defined in sRGB. If the source pixels are
  actually meant to be for example P3, stripping the profile will make viewers
  interpret the color values incorrectly.

<file>
  Any image file format that ImageMagick can handle.
  To provide filenames that start with "-", terminate options with "--", e.g.
    color-space strip -- file1 -file2-with-hyphen file3

<profile>
  Either a path to an ICC file or a name of a builtin profile.
  Named builtin profiles: P3, sRGB

-o|-output|--output <file>
  Commands that modify files accept this option, which causes the result to be
  written to the specified output file instead of the input file.
```

For example, if you are designing in P3 in Figma and export a PNG, the resulting PNG file will
not have the Display P3 profile embedded which means it will incorrectly be interpreted as sRGB
and your colors will not look right. Say you intend to display this image in a web browser, then
you will need to produce both a file with the P3 profile and a file converted to sRGB
(for browsers without P3 support.) You'd do the following:

1. Assign the P3 profile: `color-space assign p3 image.png`
2. Convert & generate sRGB variant: `color-space convert srgb image.png -o image.srgb.png`
3. Use the `<picture>` element to provide the browser with the two images:

```html
<picture>
  <source media="(color-gamut: p3)" srcset="image.png">
  <img src="image.srgb.png" alt="Drawing of a cat">
</picture>
```


## autorun

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
Usage: autorun [options] <file> ... [-- <cmdpattern>]
options:
  -h[elp]             Show description and detailed help for this program.
  -v[erbose]          Print some details to stdout.
  -s[top]             Exit with an error if <file> disappears from the file system.
  -c[lear]            Clear terminal screen before (re)starting the command.
  -no-clear           Disable clear even when stdio are TTYs.
  -color              Enable stylized output even if stdio is not TTY.
  -no-color           Disable stylized output even if stdio is TTY.
  -r[eact-in]=<msec>  React within <msec> to file changes. Defaults to 100.
  -no-banner          Don't print banner to stdout when restarting the command.

<file>        Files to watch for changes

<cmdpattern>  Pattern for command to execute, replacing ":" with <file>.
              If not specified or if <cmdpattern> does not include a ":"
              argument, <file> is executed with <cmdpattern> as arguments.
              If you need to pass an argument of value ":", use "\" which
              will be expanded to ":" rather than <file>.

Examples:
  autorun foo.sh                      # foo.sh
  autorun foo.sh vars.sh              # foo.sh
  autorun foo.sh -- : bar             # foo.sh bar
  autorun foo.js -- node --check :    # node --check foo.js
  autorun foo.py -- python : \\: a    # python foo.py : a
```
