# Submodule
This repository uses a submodule to pull in reveal.js, so after cloning you still need to pull in the submodule. If you don't do this the presentation will not have any layout.

```bash
git submodule update --init
```
# Start

To start the presentation, open `index.html` in your webbrowser.

# Build

When making changes to the sourcefile (`index.md`) you need to rebuild the
presentation. To do this make sure you have installed
[Pandoc](http://pandoc.org/).

Then run:

```
./build
```
