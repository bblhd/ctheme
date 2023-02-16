# ctheme
Minimalist runtime color based theming engine library, designed to allow the syncing
and runtime configuration of colors across different lightweight programs, such as
terminals, text editors, file browsers, and window managers.

## Usage
To use this library, you have to compile and link `ctheme.c` with the main program,
and then include `ctheme.h` wherever the library is used.

Interfacing with the library is done using the functions `ctheme_clear`, `ctheme_load`, `ctheme_get`,
and `ctheme_set`. Their function protypes are as follows:

- `void ctheme_clear();`
- `int ctheme_load(char *path);`
- `color_t ctheme_get(colorscheme_id_t id, colorscheme_level_t level, color_format_t format);`
- `void ctheme_set(colorscheme_id_t id, colorscheme_level_t level, color_t color, color_format_t format);`

### ctheme_clear
`ctheme_clear` resets all colors to blank, and sets up all initial inheritance rules.
It should be called at least once before using any other library functions, or most colors
will not work correctly.

### ctheme_load
`ctheme_load` will load a configuration file from the given path and cache all of the
information contained within. If the path is relative, then it is assumed to be located
in the user's home folder. If the path given is null, then it will attempt to load
from a file named `.ctheme` in the current user's home folder. 

If no configuration file is found or it cannot be accessed, the function returns zero,
otherwise it will return non-zero.

### ctheme_get
`ctheme_get` will find a requested color and then return it in the given format.

All allowed values of `id` are listed in the header file and take the form of `COLORSCHEME_*`, where `*`
is substituted for its name in all caps, such as `COLORSCHEME_DEFAULT` or `COLORSCHEME_RED`.

`level` represents roughly what element is being given this color. For example, in the case of
`COLORSCHEME_DEFAULT` and `COLORSCHEME_SELECTED`, a level of 1 is the foreground color,
2 is the foreground color, 3 is the border color, and 4 is window manager decorations.
Hypothetically, the level can go up to 255, although there are only 4 functionally distinct
values currently. In `COLORSCHEME_DARK` through `COLORSCHEME_MAGENTA`, level 1 should represent a darker
version of the color, and level 2 should be a lighter version. This is for use in terminals.

The format specifies whether a dummy alpha channel should be provided and in what order
the color channels should be in. The format names (`RGB`, `BGR`, `RGBA`, `BGRA`, `ARGB`, and `ABGR`)
are written in little-endian, meaning that the color red using `RGB` would return `0x0000ff`,
whereas `BGR` would return 0xff0000.

### ctheme_set
`ctheme_set` will change a requested color to a given color literal. It is intended to be used to
set fallback colors should no configuration files be found.

All of the parameters that are shared in common with `ctheme_get` mean the same thing. The main difference
is that the color format is used for the input color instead of the output. The `color` parameter must be given in one of the 6 available formats.

### Configuration file

A ctheme configuration file consists of a series of entries seperated by line breaks.
Each entry is of the form:

```
<name> <color>...
```

The `name` corresponds to a specific colorscheme ID, and each successive color represents
a different level for that ID. If the list of colours stops prematurally, attempts to access
levels beyond will redirect to the first level.

Each provided `color` can either be a hex code, or the name of another colorscheme ID with
an optional number used to indicate which level to get from. If the number is absent, it will use
its own level number instead.

The following is an example .ctheme file, demonstrating some features of the syntax.

```
default   #000000   #ffffff  #000000  #555555
selected  #ffffff   #0000ff  #ababab  #0000ff

dark      #000000   #555555
light     #ababab   #ffffff
red       #ff0000
yellow    #ffff00
green     #00ff00
cyan      #00ffff
blue      #0000ff
magenta   #ff00ff

numbers   cyan1     default
strings   yellow1   default

operators green1    default
keywords  operators
comments  light1    default
special   purple1   default

constants default
variables default
arrays    default
functions default
classes   default
```
