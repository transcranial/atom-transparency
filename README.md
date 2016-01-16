# How To Achieve Atom Editor Transparency

In atom, there is no easy config (yet) to set window or background transparency as you would in iTerm or TextMate. Here's a straightforward guide on how to achieve transparent window awesomeness. I'm running Mac OSX. 

<p align="center">
  <img src="screenshot.png" />
</p>

Atom must be built from source with 2 additional lines of code. This makes Atom run as a frameless window which allows transparency to be enabled within Electron. After [cloning or forking Atom](https://github.com/atom/atom), insert [these two lines](https://github.com/transcranial/atom/blob/master/src/browser/atom-window.coffee#L27-L28) in `src/browser/atom-window.coffee`, 

changing this:

```coffeescript
options =
  show: false
  title: 'Atom'
  'web-preferences':
    'direct-write': true
```

to this:

```coffeescript
options =
  frame: false
  transparent: true
  show: false
  title: 'Atom'
  'web-preferences':
    'direct-write': true
```

Then run:

```sh
./script/clean

./script/build
```

This can take awhile, but once complete, fire up Atom, open up your editor LESS stylesheet (`⌘-shift-p`, then `Application: Open Your Stylesheet`), and add the following CSS. This is a basic guide - you can experiment with your own settings to get the effect you want. For example, to avoid text-on-text collisions in the autocomplete popups, I set `atom-overlay > *` to near-complete opacity.

```css
html * {
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

That's it, pretty simple!

