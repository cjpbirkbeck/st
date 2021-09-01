# st (the simple terminal)
Here is a patched version of the [simple terminal](http://st.suckless.org/) by [suckless](http://www.suckless.org/), "a simple terminal emulator for X which sucks less". Generally, I mostly use it with [tmux](https://github.com/tmux/tmux/wiki), but it works perfectly fine without it.

## Requirements

* libX11: Basic Xorg headers
* libXft: font rendering libraries
* libXcursor: for a themed cursor

## Installation

Edit config.mk to match your local setup (st is installed into the /usr/local namespace by default).

Afterwards enter the following command to build and install st (if necessary as root):

`make clean install`

### Nix/NixOS

For users of either [the nix package manager or NixOS](https://www.nixos.org) can use the following default.nix to compile st into the nix store with `nix-build`. Based of an old version of [the default in nixpkgs](https://github.com/NixOS/nixpkgs/blob/master/pkgs/applications/terminal-emulators/st/default.nix), so probably not the best version of this code but it works.

```nix
rec {
  pkgs = import <nixpkgs> {};

  patches = [];
  conf = null;
  extraLibs = [];
  libX11 = pkgs.xorg.libX11;
  libXft = pkgs.xorg.libXft;
  libXcursor = pkgs.xorg.libXcursor;

  cjpb-st = with pkgs; stdenv.mkDerivation rec {
    name = "st-0.8.3";

    src = ./.;

    inherit patches;

    nativeBuildInputs = [ pkgconfig ncurses ];
    buildInputs = [ libX11 libXft libXcursor ] ++ extraLibs;

    preBuild = ''
      sed -i -e '/share\/applications/d' Makefile
    '';

    installPhase = ''
      TERMINFO=$out/share/terminfo make install PREFIX=$out
    '';
  };
}
```

To install st in the nix store in NixOS, you can add the following override:

```nix
{ pkgs, config, ... }:

{
  nixpkgs.config.packageOverrides = pkgs: with pkgs; rec {
    st_patched = st.overrideAttrs ( oldAttr: rec {
      src = fetchFromGitHub {
        owner = "cjpbirkbeck";
        repo = "st";
        # Replace this with the current revision!
        rev = "0000000000000000000000000000000000000000";
        # Likewise, replace this with current sha256!
        sha256 = "0000000000000000000000000000000000000000000000000000";
      };

      buildInputs = with pkgs.xorg; [ libX11 libXft libXcursor ];

      preBuild = ''
        sed -i -e '/share\/applications/d' Makefile
      '';

      installPhase = ''
        TERMINFO=$out/share/terminfo make install PREFIX=$out

        mkdir -p $out/share/applications/
        install -D st.desktop $out/share/applications/
      '';
    } );
  };
}
```

## Patches

The following patches have been applied to vanilla st 0.8.3:

* [Alpha Focus Highlight]( ./applied/st-alphaFocusHighlight-20200216-26cdfeb.diff ): If a compositor like [picom](http://github.com/yshui/picom) is enabled, a terminal with focus will have a set opacity, while all others will have another opacity.
* [Anygeomtry](./applied/st-anygeometry-0.8.1.diff): Set st window sizes by pixel size, with the argument `-G [width[xheight]]`.
* [Anysize]( ./applied/st-anysize-0.8.1.diff ): Dynamically set the inner border size and centers the terminal if the windows is not a multiple of the character size and inner border size. Useful for users of tiling window managers like [dwm](https://dwm.suckless.org), [awesomewm](https://www.awesomewm.org), [i3wm](https://i3wm.org), etc.
* [Blinking cursor]( ./applied/st-blinking_cursor-20200531-a2a7044.diff ): Makes the cursor blink, like the terminals of yore.
* [Boxdraw]( ./applied/st-boxdraw_v2-0.8.3.diff ): Allows for the drawing of Unicode line characters, allowing programs like tmux to draw lines without gaps.
* [Clipboard]( ./applied/st-clipboard-0.8.3.diff ): Use Xorg's clipboard, not primary.
* [Font2]( ./applied/st-font2-20190416-ba72400.diff ): Allows for different fonts to be used as "backup". Currently, I have not enabled any so far.
* [Netwmicon]( ./applied/st-netwmicon-0.8.4.diff ): Hardcodes a default icon.
* [Newterm]( ./applied/st-newterm-0.8.2.diff ): Using the keybinding of `C-S-Return` to open a new terminal with the present working directory as the current instance.
* [OSC-10-11-12]( ./applied/st-osc_10_11_12-20200418-66520e1.diff ): Enables those escape codes for modifying the background colours.
* [Scrollback]( ./applied/st-scrollback-20200419-72e3f6c.diff ): Enable a scrollback buffer by default …
* [Scrollback mouse]( ./applied/st-scrollback-mouse-20191024-a2c479c.diff ): … and allow it to scroll that buffer with the scrollwheel (or buttons in the case of trackpoint). Note that I have disable the middle click and made scrolling with the scrollwheel default, without needing to press shift.
* [Themed cursor]( ./applied/st-themed_cursor-0.8.1.diff ): Uses the cursor from the cursor theme. Requires `libXcursor`.
* [Undercurl](./applied/st-undercurl-0.8.4.diff): This patch adds support for curly underlining, such as vim's spelling correction.
* [Vertcenter]( ./applied/st-vertcenter-20180320-6ac8c8a.diff ): Center oversized lines horizontally.
* [Workingdir]( ./applied/st-workingdir-20200317-51e19ea.diff ): Specify a working directory with `-d [path]`. Path can be absolute or relative.

### Theming

Colours are based off of the [Oceanic Material](https://github.com/rahulpatel/oceanic-material-iterm) theme from [iTerm2 Color Schemes](https://github.com/mbadolato/iTerm2-Color-Schemes/). Specifically, it comes from a conversion the termite theme with [terminal.sexy](https://www.terminal.sexy).

Not that I have changed some of the colours to be easier to read. Specifically, color5 (normal Magenta) was originally `#8800a0` and color7 (normal White) was `#a4a4a4` and color13 (bright Magenta) was originally `#aa4abc`.

|Colours|  Normal   |  Bright   |
|-------|-----------|-----------|
|Black  | `#000000` | `#777777` |
|Red    | `#ee2b2a` | `#dc5c60` |
|Green  | `#40a33f` | `#70be71` |
|Yellow | `#ffea2e` | `#fff163` |
|Blue   | `#1e80f0` | `#54a4f3` |
|Magenta| `#c900ed` | `#C585d1` |
|Cyan   | `#16afca` | `#42c7da` |
|White  | `#e3e3e3` | `#ffffff` |

Background = `#1c262b`

Foreground = `#c2c8d7`

## Running st

If you did not install st with make clean install, you must compile the st terminfo entry with the following command:

`tic -sx st.info`

See [the man page](./st.1) for additional details.

## TODO

* Better integration with tmux, maybe using externalpipe?

## License

See [LICENSE](./LICENSE) for details.

## Credits
Based on Aurélien APTEL <aurelien dot aptel at gmail dot com> bt source code.

### Patch credits
* [Alpha Focus Highlight]( ./applied/st-alphaFocusHighlight-20200216-26cdfeb.diff ): Julius Hülsmann
* [Anygeomtry](./applied/st-anygeometry-0.8.1.diff): José Miguel Sánchez García
* [Anysize]( ./applied/st-anysize-0.8.1.diff ): Augusto Born de Oliveira
* [Blinking cursor]( ./applied/st-blinking_cursor-20200531-a2a7044.diff ): Genki Sky, Steve Ward, jvden
* [Boxdraw]( ./applied/st-boxdraw_v2-0.8.3.diff ): [Avi Halachmi](https://github.com/avih)
* [Clipboard]( ./applied/st-clipboard-0.8.3.diff ): Kai Hendry, Laslo Hunhold, Matthew Parnell
* [Font2]( ./applied/st-font2-20190416-ba72400.diff ): Kirill Bugaev
* [Netwmicon]( ./applied/st-netwmicon-0.8.4.diff ): Aleksandrs Stier
* [Newterm]( ./applied/st-newterm-0.8.2.diff ): Matías Long, Stein Bakkeby (orphan version)
* [OSC-10-11-12]( ./applied/st-osc_10_11_12-20200418-66520e1.diff ): Christian Tenllado
* [Scrollback]( ./applied/st-scrollback-20200419-72e3f6c.diff ): Jochen Sprickerhof, M Farkas-Dyck, Ivan Tham, Ori Bernstein, Matthias Schoth, Paride Legovini, Lorenzo Bracoo, Kamil Kleban, Avi Halachmi, Jacob Prosser
* [Themed cursor]( ./applied/st-themed_cursor-0.8.1.diff ): Jim Fowler
* [Undercurl](./applied/st-undercurl-0.8.4.diff): [HexOctal](https://github.com/hexoctal)
* [Vertcenter]( ./applied/st-vertcenter-20180320-6ac8c8a.diff ): Doug Whiteley
* [Workingdir]( ./applied/st-workingdir-20200317-51e19ea.diff ): David Gričar
