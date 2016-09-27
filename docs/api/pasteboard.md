# Pasteboard

> Access to the macOS Pasteboard

## Examples

The `Pasteboard` class is only available in the main process, but you can also use it
in the render process via the [`remote`](remote.md) module.

You can get an instance by asking the app object for a named pasteboard. 

### Names

In macOS, there are 5 standard pasteboards, however you can use any string 
for your own. These are: `"general"`, `"font"`, `"ruler"`, `"find"` and `"drag"`.  

### Main process

An example of setting the find pasteboard to a string:

```javascript
const {Pasteboard} = require('electron')

if (process.platform === 'darwin') {
  const pasteboard = require('electron').remote.app.pasteboardWithName("find")
  pasteboard.write("Danger, danger")
}
```

An example of pulling out the results of the general pasteboard to a string:

```javascript
const {Pasteboard} = require('electron')

if (process.platform === 'darwin') {
  const pasteboard = require('electron').remote.app.pasteboardWithName("general")
  const copied_text = pasteboard.read()
}
```

An example for keeping track of changes to the pasteboard:

```javascript
const {Pasteboard} = require('electron')

if (process.platform === 'darwin') {
  const pasteboard = require('electron').remote.app.pasteboardWithName("find")
  pasteboard.registerForChanges((value) => {
      console.log(["A new value was found in the find pasteboard", value])
  })
}
```

### Render process

TODO: no idea how this works yet, but will need to support something like it

Below is an example of creating a menu dynamically in a web page
(render process) by using the [`remote`](remote.md) module, and showing it when
the user right clicks the page:

```html
<!-- index.html -->
<script>
const {remote} = require('electron')
const {Menu, MenuItem} = remote

const menu = new Menu()
menu.append(new MenuItem({label: 'MenuItem1', click() { console.log('item 1 clicked') }}))
menu.append(new MenuItem({type: 'separator'}))
menu.append(new MenuItem({label: 'MenuItem2', type: 'checkbox', checked: true}))

window.addEventListener('contextmenu', (e) => {
  e.preventDefault()
  menu.popup(remote.getCurrentWindow())
}, false)
</script>
```

## Class: Pasteboard

### Instance Methods

The `pasteboard` object has the following instance methods:

#### `pasteboard.set(string)`

* `string` String The test that you want to set in the pasteboard`.

#### `pasteboard.contentsAsString()`

Gets the contents of the pasteboard as a string, can return null if
the content does not have a string representation. 

#### `pasteboard.clear()`

Empties the pasteboard's contents.

#### `pasteboard.registerForChanges(function)`

This starts a host app background thread that keeps tracking the pasteboard
for changes, and will send an event along when the clipboard's string contents
have changed.
