# How To Achieve Atom Editor Transparency

In atom, there is no easy config (yet) to set window or background transparency as you would in iTerm or TextMate. Here's a straightforward guide on how to achieve transparent window awesomeness.

This has been tested on both macOS and Ubuntu 14.04 desktop, for Atom versions 1.0 up through 1.11.

<p align="center">
  <img src="screenshot.png" />
</p>

Atom must be built from source with 2 additional lines of code. This makes Atom run as a frameless window which allows transparency to be enabled within Electron. After [cloning or forking Atom](https://github.com/atom/atom), add the following to `options`:

```coffeescript
frame: false
transparent: true
```

in `src/browser/atom-window.coffee` (pre-v1.9) or `src/main-process/atom-window.coffee` in versions 1.9+,

changing this:

```coffeescript
options =
  show: false
  title: 'Atom'
  backgroundColor: "#fff"
  ...
```

to this:

```coffeescript
options =
  frame: false
  transparent: true
  show: false
  title: 'Atom'
  #backgroundColor: "#fff"
  ...
```

Note `backgroundColor` is commented out.

Then run:

```sh
./script/clean && ./script/build
```

Refer to the official [build guides](https://github.com/atom/atom#building) for additional instructions if necessary. You may want to build a debian package, for example.

This can take awhile, but once complete, fire up Atom.

**On linux, add an additional `--enable-transparent-visuals` flag while starting atom.**

**In Atom v1.7+, atom must be started with an additional `--disable-gpu` flag.** Otherwise, there will be a lot of UI flickering.

Open up your editor LESS stylesheet (`⌘-shift-p` or `ctrl-shift-p`, then `Application: Open Your Stylesheet`), and add the following CSS. This is a basic guide - you can experiment with your own settings to get the effect you want. For example, to avoid text-on-text collisions in the autocomplete popups, I set `atom-overlay > *` to near-complete opacity.

```css
html, html * {
  background: rgba(0, 0, 0, 0) !important;
}

atom-pane, atom-panel, atom-notification {
  background: rgba(0, 0, 0, 0.5) !important;
}

atom-overlay > * {
  background: rgba(0, 0, 0, 0.9) !important;
}

atom-text-editor::shadow {
  .cursor-line {
    background-color: rgba(0, 0, 0, 0.2) !important;
  }
  .selection .region {
    background-color: rgba(0, 0, 0, 0.2) !important;
  }
  .gutter {
    background-color: rgba(0, 0, 0, 0) !important;
  }
}
```

In the CSS above, this works pre-v1.9:

```css
html * {
  background: rgba(0, 0, 0, 0) !important;
}
```

but for v1.9+, this must be:

```css
html, html * {
  background: rgba(0, 0, 0, 0) !important;
}
```

For Macs experiencing issues with shadow (burn in) text described in issue [#5](https://github.com/transcranial/atom-transparency/issues/5) add the following lines to `atom-pane`:
```css
atom-pane, atom-panel, atom-notification {
  background: rgba(0, 0, 0, 0.5) !important;
  -webkit-backface-visibility: hidden; /* Chrome, Safari, Opera */
  backface-visibility: hidden;
}
```
Then turn off background-tips addon.

That's it--pretty simple!
