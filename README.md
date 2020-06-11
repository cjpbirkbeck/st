Here is patched version of the [simple terminal](http://st.suckless.org/) by [suckless](http://www.suckless.org/)

# Patches

The following patches have been applied to vanilla st 0.8.3:

* [Alpha Focus Highlight]( st-alphaFocusHighlight-20200216-26cdfeb.diff ): If composting is enabled, have focused terminal have opacities while unfocused terminals have another.
* [Anysize]( st-anysize-0.8.1.diff ): creates a border so
* [Blinking cursor]( st-blinking_cursor-20200531-a2a7044.diff ): a previous version of the patch did not really work, and this one needed whole `for` loop replaced; see the diff for details.
* [Boxdraw]( st-boxdraw_v2-0.8.3.diff ): Allows the drawing of Unicode line character, allowing programs like tmux to draw lines without gaps.
* [Clipboard]( st-clipboard-0.8.3.diff ): Use Xorg's clipboard, not primary.
* [Font2]( st-font2-20190416-ba72400.diff ): Allows for different fonts to be used as "backup". Note that currently, I have not enabled any so far.
* [Newterm]( st-newterm-0.8.2.diff ): Use the keybinding of `C-S-Return` to open a new terminal with the present working directory as the current instance.
* [OSC-10-11-12]( st-osc_10_11_12-20200418-66520e1.diff ): Enables those escape codes for modifying the background colours.
* [Scrollback]( st-scrollback-20200419-72e3f6c.diff ): Enable a scrollback buffer by default.
* [Scrollback mouse]( st-scrollback-mouse-20191024-a2c479c.diff ): ... and allow it to scroll that buffer with the scrollwheel (or buttons in the case of trackpoint). Note that I have disable the middle click and made scrolling with the scrollwheel default, without needing to press shift.
* [Vertcenter]( st-vertcenter-20180320-6ac8c8a.diff ): Center oversized lines horizontally.
* [Workingdir]( st-workingdir-20200317-51e19ea.diff ): Specify a working directory with -d [path]. Path can be absolute or relative.

# TODO

* Enable terminal to display icon in Xorg
* Enable themed mouse pointer. Currently, it seems like it default to the I-bar, regardless.
* Better integration with tmux, maybe using externalpipe?

Distributed with same license. Original readme to follow.

```text
st - simple terminal
--------------------
st is a simple terminal emulator for X which sucks less.


Requirements
------------
In order to build st you need the Xlib header files.


Installation
------------
Edit config.mk to match your local setup (st is installed into
the /usr/local namespace by default).

Afterwards enter the following command to build and install st (if
necessary as root):

    make clean install


Running st
----------
If you did not install st with make clean install, you must compile
the st terminfo entry with the following command:

    tic -sx st.info

See the man page for additional details.

Credits
-------
Based on Aurélien APTEL <aurelien dot aptel at gmail dot com> bt source code.
```
