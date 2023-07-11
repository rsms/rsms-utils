# rsms-utils

Collection of command-line programs to help with everyday computer life.

- [autorun](bin/autorun) — Run a command when some files changes
- [color-space](bin/color-space) — Inspect and convert color spaces of images
- [logbook](bin/logbook) — Manage a simple journal per project
- [lserrno](bin/lserrno) — List & search libc error codes along with description
- [mkgif](bin/mkgif) — Creates an animated GIF from a video file

**Installation:** The idea is to add these tools to your `PATH`. Something like this:

```
cd ~/src
git clone https://github.com/rsms/rsms-utils.git
# edit your ~/.bashrc or ~/.zshrc shell file, adding:
export PATH=$PATH:$HOME/src/rsms-utils/bin
```


## Usage

See each program's help with `program --help`

Some notes:

### color-space

For example, if you are designing in P3 in Figma and export a PNG, the resulting PNG file will not have the Display P3 profile embedded which means it will incorrectly be interpreted as sRGB and your colors will not look right. Say you intend to display this image in a web browser, then you will need to produce both a file with the P3 profile and a file converted to sRGB (for browsers without P3 support.) You'd do the following:

1. Assign the P3 profile: `color-space assign p3 image.png`
2. Convert & generate sRGB variant: `color-space convert srgb image.png -o image.srgb.png`
3. Use the `<picture>` element to provide the browser with the two images:

```html
<picture>
  <source media="(color-gamut: p3)" srcset="image.png">
  <img src="image.srgb.png" alt="Drawing of a cat">
</picture>
```
