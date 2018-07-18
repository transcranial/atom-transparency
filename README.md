# How To Achieve Atom Editor Transparency

In atom, there is no easy config (yet) to set window or background transparency as you would in iTerm or TextMate. Here's a straightforward guide on how to achieve transparent window awesomeness.

This has been tested on both macOS and Ubuntu 14.04 desktop, for Atom versions 1.0 up through 1.28.

<p align="center">
  <img src="screenshot.png" />
</p>

Atom must be built from source with 2 additional lines of code. This makes Atom run as a frameless window which allows transparency to be enabled within Electron. After [cloning or forking Atom](https://github.com/atom/atom), and do one of the following, depending on which files you have.

- (v1.28+) edit `src/main-process/atom-window.js`:

  ```diff
   const options = {
     show: false,
     title: 'Atom',
     tabbingIdentifier: 'Atom',
  +  transparent: true,
     webPreferences: {
       ...
  ```
  
- (v1.9+) edit `src/main-process/atom-window.coffee`:

  ```diff
   options =
     show: false
  +  frame: false
  +  transparent: true
     title: 'Atom'
  -  backgroundColor: "#fff"
  +  #backgroundColor: "#fff"
  ```
  
- (pre-v1.9) add the following to `options` in `src/browser/atom-window.coffee`:

  ```coffeescript
  frame: false
  transparent: true
`  ``

Then run:

```sh
./script/clean && ./script/build
```

Refer to the official [build guides](https://github.com/atom/atom#building) for additional instructions if necessary. You may want to build a debian package, for example.

This can take awhile, but once complete, fire up Atom.

**On Linux, add an additional `--enable-transparent-visuals` flag while starting Atom.** You can easily do this via:

```sh
echo '--enable-transparent-visuals' > ~/.config/atom-editor-flags.conf
```

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

That's it--pretty simple!
