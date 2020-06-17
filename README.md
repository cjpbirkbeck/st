Here is patched version of the [simple terminal](http://st.suckless.org/) by [suckless](http://www.suckless.org/), "a simple terminal emulator for X which sucks less".

# Requirements

* libX11: Basic Xorg headers
* libXft: font rendering libraries

The [cursor theming patch](./unapplied/st-themed_cursor-0.8.1.diff) might require additional libraries.

# Installation

Edit config.mk to match your local setup (st is installed into the /usr/local namespace by default).

Afterwards enter the following command to build and install st (if necessary as root):

`make clean install`

# Patches

The following patches have been applied to vanilla st 0.8.3:

* [Alpha Focus Highlight]( ./applied/st-alphaFocusHighlight-20200216-26cdfeb.diff ): If composting is enabled, have focused terminal have opacities while unfocused terminals have another.
* [Anysize]( ./applied/st-anysize-0.8.1.diff ): creates a border so
* [Blinking cursor]( ./applied/st-blinking_cursor-20200531-a2a7044.diff ): a previous version of the patch did not really work, and this one needed whole `for` loop replaced; see the diff for details.
* [Boxdraw]( ./applied/st-boxdraw_v2-0.8.3.diff ): Allows the drawing of Unicode line character, allowing programs like tmux to draw lines without gaps.
* [Clipboard]( ./applied/st-clipboard-0.8.3.diff ): Use Xorg's clipboard, not primary.
* [Font2]( ./applied/st-font2-20190416-ba72400.diff ): Allows for different fonts to be used as "backup". Note that currently, I have not enabled any so far.
* [Newterm]( ./applied/st-newterm-0.8.2.diff ): Use the keybinding of `C-S-Return` to open a new terminal with the present working directory as the current instance.
* [OSC-10-11-12]( ./applied/st-osc_10_11_12-20200418-66520e1.diff ): Enables those escape codes for modifying the background colours.
* [Scrollback]( ./applied/st-scrollback-20200419-72e3f6c.diff ): Enable a scrollback buffer by default …
* [Scrollback mouse]( ./applied/st-scrollback-mouse-20191024-a2c479c.diff ): … and allow it to scroll that buffer with the scrollwheel (or buttons in the case of trackpoint). Note that I have disable the middle click and made scrolling with the scrollwheel default, without needing to press shift.
* [Vertcenter]( ./applied/st-vertcenter-20180320-6ac8c8a.diff ): Center oversized lines horizontally.
* [Workingdir]( ./applied/st-workingdir-20200317-51e19ea.diff ): Specify a working directory with `-d [path]`. Path can be absolute or relative.

## Theming

Colours come from the [Oceanic Material](https://github.com/rahulpatel/oceanic-material-iterm) theme from [iTerm2 Color Schemes](https://github.com/mbadolato/iTerm2-Color-Schemes/). Specifically, it comes from conversion the termite theme with [terminal.sexy](https://www.terminal.sexy)

# Running st

If you did not install st with make clean install, you must compile the st terminfo entry with the following command:

`tic -sx st.info`

See [the man page](./st.1) for additional details.

# TODO

* Set a default icon.
* Enable themed mouse pointer. Currently, it seems like it default to the I-bar, regardless of the state of the terminal.
* Better integration with tmux, maybe using externalpipe?

# License

See [LICENSE](./LICENSE) for details.

Credits
-------
Based on Aurélien APTEL <aurelien dot aptel at gmail dot com> bt source code.
