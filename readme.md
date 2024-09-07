# KRPano Bundler Project
## Description

This is a minimalistic demo project that uses bundler to create krpano apps

## Requirements
- local php server to run

## Project folder layout
- `_app` - folder to keep bundled kml, js and css files as well as plugins, fonts and other assets related to core app functionality. This folder should not contain actual content and be essentially a core for running supported virtual tours
- `_dev` - folder for source code in kml, js, css and fonts. It also has php files for bundler and bundler code itself
- `tour` - should contain virtual tour content (xml, panos, scripts specific to this tour and other content-specific assets unique to each project). It can has any name (see usage). There could be multiple different tour folders alongside `_app` and `_dev` folder. All tours will use the same core to run
- `index.php` on top level for running bundled code with tour from `tour` folder as default option

## Usage
Four ways to load the project:
- `http://localhost:port/project_folder_name/_dev/?tour=tour` will run bundler and load tour from `tour` folder. **? tour=** key can take any folder with tour inside project folder
- `http://localhost:port/project_folder_name/_dev` will run bundler and load tour from `tour` folder. A default option.
- `http://localhost:port/project_folder_name/tour` will open tour from `tour` folder with latest bundled core.
- `http://localhost:port/project_folder_name` will open tour from `tour` folder with latest bundled core

## How bundler works
- bundler reads all .kml files from `./_dev/src` folder that is designated to kml code, merges them to one .xml file and saves to `./_app/xml/app.xml`. This file can be safely encrypted
- bundler reads all JS files from `./_dev/js` folder, merges them and saves to `./_app/js/js.js`
- bundler reads all CSS files from `./_dev/CSS` folder, merges them and saves to `./_app/css/css.css`
These three files represent core code and styles

The `tour.xml` file should have `<include url="../_app/include.xml%$_timestamp%" />` include line in it to load core files

## Additional modules
### `./_dev/src/devlib4.kml` 
A developer library that contains the following actions and prototypes:
- `get_this` puts krpano object (hotspot, layer or plugin) to `this` alias if called from this object scope. Takes no arguments.
- `array_push(array_name, array_item_name)` kml action that adds new item with name = array_item_name to krpano array array_name.
- `remove_array(array_name)` kml action that removes all items from array named array_name
- anonymous function to set `window.krpano` global variable in window object so all krpano functionality becomes available anywhere in JS code
- `window._k` object with a lot of useful abbreviations for window.krpano object internal methods
- `Invisible_Content` prototype to handle krpano objects (hotspot, layer, plugin) visibility with lots useful methods allowing to build interactions related to visibility. Makes object invisible by default
- `Visible` prototype paired with `Invisible_Content` will make an inheriting object visible by default
- `new` action to create new objects from prototypes (requires constructor method in prototype, constructor name should be equal to prototype name)
- `newhotspot` and `newlayer` actions to create new objects, should be used inside constructor, see implementation in `./_dev/src/menu/prototypes.kml`

### `./_dev/src/common.kml` 
A file designated to keep common actions and constants for app core. Contains template for creating krpano.inlinefunctions and `ui_reaction_delay` variable

### `./_dev/src/startup.kml` 
This file is entry point to launch tour and should contain all auto running actions related to tour startup.

### `./_dev/js/common.js` 
A file with some useful functions:
-`setCookie` and `getCookie` function to handle cookies,
- `setKRPanoConsole` sets krpano console to more readable look, is called from startup.kml
- `roundVal` JS function to round numbers. Difference from `toFixed` is `roundVal` returns number while `toFixed` returns string
- `readURL` reads url keys and sets `krpano.is_bundler`,  `krpano.devmode`, `krpano.logkey`, `krpano.showerrors` variables
- `loadJSFile(path)` function loads js file asynchronously
- `loadjscssfile(filename, filetype, onload)` loads js or css file synchronously
- `hyphenate(text)` hyphenates cyrillic text
- `getRandomInt(min, max)` return secure random number in [min, max] range
- `suggestPassword` users `getRandomInt` to create secure 8 letter password combined from [a-zA-Z0-9] characters
- `setPageRatio(ratio)` to set `#pano` div ratio (useful for taking screenshots)
- `initResizeObserver(id, lName, kCallback)` - uses ResizeObserver browser api to monitor dom objects size change. Useful for html code inside textfields or container sprites. `Id` is id for top level dom object to observe, `lName` is name of krpano layer, `kCallback` is name of callback function in `lName` layer (typically `resize_observer_callback`)
- `unobserve` unobserves `dom_object#id`
- `kArrayPush(arrayName, arrayItemName, parameters)` function to create krpano array objects with specified parameters. 
`parameters = [{name, value}, ...]`
- `injectCSS(css)` used to inject css to page from within krpano js actions
- `hexToRgb(hex)`, `componentToHex(c)`, `rgbToHex(r, g, b)` function to convert color values to hex or rgb
- `clipboard(content)` copy text to clipboard
- `getAngularDistance(pointA, pointB)` - gets shortest angular distance between two points on circle
- `normalizeAngle360(angle)`, `normalizeAngle180(angle)` normalizes angles to [0, 360) and (-180, 180] respectively
- `degToRad(angle)`, `radToDeg(angle)` to convert angles to radians or degrees
- `uiTimeout(callback)` useful function to delay button actions to implement 'wait for completion' principle for hovered and active button states
- `getTimestamp` returns current time in seconds converted to base 36 number
- `slowCallwhen(condition_func, callback)` creates interval that checks condition_func to return true and when it's true runs callback

### `./_dev/js/vkbeautify.js` and  `./_dev/js/xml2json.min.js` 
Files used by bundler to convert xml to js object and back with xml formatting
