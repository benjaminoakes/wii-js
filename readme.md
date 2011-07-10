wii-js
==============================================================================================
The Nintendo Wii is an entertainment system with an utterly _massive_ install base, and when 
you couple it with the fact that it's got a web browser (mostly) built in, there's a lot of
potential for third party development. Sadly, few have opted to do any sort of development for
it. While it doesn't help that Nintendo pretty much dropped the ball on this opportunity, the
experience of browsing the web on the Wii isn't actually that compelling to begin with.

That said, I think this can serve one other purpose: it's an ideal environment to teach children
how to program! I created this library to sanitize Wii interaction with webpages in the browser,
as it's notoriously crippled. It aims to offer a solid, documented, performant API that's easy to 
understand and pick up. With this library, you can have up to 4 Wii-motes interacting with your
webpage at once, a dynamic not found in other web browsing mediums.

You can find a built source file and a _minified_ source file for production use in the **/js/** directory.
To play with a live example, load up the demo (_index.html_) on your own server, or feel free to use mine:

**wii-js Demo: [http://venodesigns.net/wii/](http://venodesigns.net/wii/)**  

Questions, comments, criticism and praise can be directed to me at the following outlets:

- You can email me at **ryan [at] venodesigns (dot) net**.  
- You can hit me up on Twitter: **[@ryanmcgrath](http://twitter.com/ryanmcgrath/)**  
- Contact me through **[my website](http://venodesigns.net)**  
- **Technical issues can be filed on the [wii-js GitHub Issues Tracker](https://github.com/ryanmcgrath/wii-js/issues)**  


Example Usage
----------------------------------------------------------------------------------------------
``` javascript
var wiimote = new Wii.Remote(1, {horizontal: true}),
    wiimote2 = new Wii.Remote(2, {horizontal: true});

wiimote.when('pressed_a', function() {
    alert('Wiimote #1 pressed the A Button!');
});

wiimote2.when('pressed_a', function() {
	alert('Wiimote #2 pressed the A Button!');
});
```


Technical Documentation
----------------------------------------------------------------------------------------------
The largest issue with making interactive pages that work with the Wii has been that the API has
been nothing short of a black hole. When you actually begin to dig in and figure out what's going on,
it gets even uglier to see - the primary wiimote, for instance, has a totally different set of signals
than the other three.

wii-js abstracts away most of these differences and/or problems, and works on a very simple event-dispatch 
system. What this means is that you create an instance of a Wii Remote, subscribe to events, and provide a
function to get called when that event has occurred. The following syntax should explain this:

``` javascript
wiimote.when('event_name_here', function() { /* My callback function */ });
```

When instantiating a Wii Remote instance, you can choose to have the library interpret directional pad controls
in horizontal or vertical mode. You can change this at any point, if you want, by simply swapping the property.

``` javascript
var wiimote = new Wii.Remote(1, {horizontal: true}); // Horizontal controls
var wiimote = new Wii.Remote(1, {horizontal: false}); // Vertical controls

wiimote.opts.horizontal = true; // Change to horizontal scheme.
```

You can listen for the following events on all controllers:

- **pressed_up** - The up button was pressed.  
- **pressed_down** - The down button was pressed.  
- **pressed_left** - The left button was pressed.  
- **pressed_right** - The right button was pressed.  
- **pressed_a** - The A button was pressed.  
- **pressed_1** - The 1 button was pressed. (_Note: On controller 1, this triggers a menu - work in progress..._)  
- **pressed_2** - The 2 button was pressed.  
- **pressed_plus** - The plus (+) button was pressed.  
- **pressed_minus** - The minus (-) button was pressed.  
- **roll_change** - The roll of the controller (balance) changed. You can get the current roll in radians with _"this.roll"_; positive is upright, negative is the other.  
- **distance_change** - The distance of the wiimote (in meters) from the TV/sensor bar has changed. This event isn't totally reliable, but should work for most cases.  

You can listen for the following events on _extra controllers_, but not the primary controller.

- **pressed_b** - The B button was pressed.  
- **pressed_c** - The C button (on the Nunchuk) was pressed.  
- **pressed_z** - The Z button (on the Nunchuk) was pressed.  

You can also get the following properties from any Wii Remote instance; they will return "undefined" if the remote
isn't able to see the TV/sensor bar, so be sure to check this!

- **x** - The x coordinate where the Wii Remote is pointing to on the screen. Generally between 0 and 800, but can be more on wide pages.
- **y** - The y coordinate where the Wii Remote is pointing to on the screen. Odd one; can be found as low as -48, as high as the height
of the current webpage + toolbar height, if enabled. Tinker with this one for your purposes.


Extra Tips and Tricks
------------------------------------------------------------------------------------------------------------------
One semi-useful trick to point out about this library is that each of your callback functions get passed two
arguments by default - a reference to the Wiimote you're working with, and the raw Wiimote status object that the
Wii reports back to the library. You get this in a normalized fashion, instead of having to deal with the odd polling
issues present in the browser.

``` javascript
var wiimote = new Wii.Remote(1, {horizontal: false});

wiimote.when('pressed_a', function(wii_remote, wii_remote_status) {
	/*	Alert an internal confidence level provided by the Wii. */
	alert(wii_remote_status.dpdValidity);
});
```

Debugging Javascript on the Wii is also nothing short of incredibly annoying, so I've included a convenience function
for running through error messages. My typical debugging strategy with any Wii-related code would always start with
the following:

``` javascript
try {
    // Whatever function I execute
} catch(e) { Wii.util.debug(e); }
```

The rate at which this function will improve is commensurate with how much more time and effort I can afford to put
into this project. Feel free to fork/patch/enhance if you need more. ;)


Why the button limitations?
------------------------------------------------------------------------------------------------------------------
The Nintendo Wii treats the primary controller differently than the other ones, and doesn't report any action
from the Nunchuk, nor does it report when someone has pressed the "B" button on the primary controller (as it's used
for scrolling a page).

The Wii Browser also doesn't report data for Gamecube controllers, the Classic controller, or any other accessories.

It's a work in progress to see what can be done about these, but it's impossible to guarantee anything will come out
of it unless Nintendo and/or Opera can come out with something new.


Known Issues
------------------------------------------------------------------------------------------------------------------
**Primary Wiimote is a bit more responsive than the extra 3**  
This library works by polling the status of the three extra Wii-remotes in 100ms intervals and dispatching events
based on this. Anything lower than 100ms causes the Wii to run into memory limitations, and the single-threaded
nature of the browser doesn't really help this issue.

The primary Wii-remote uses an odd combination of DOM-esque callbacks; due to this, it reports _more frequently_ than
the other Wii-remotes. It's not a showstopper by any means, but for small games it would in theory give a weighted advantage.
I'll probably end up throttling this through the library by means of a flag, e.g "game_mode": true in the initial options.


Building and Developing
------------------------------------------------------------------------------------------------------------------
If you'd like to help with this library, you're more than welcome to. Simply fork it on GitHub, work away, then
issue me a pull request. I generally respond within 24 hours.

The build system here is pretty simple - edits and changes go into the /js/src/ files, and you can run

    python build.py  

from the main directory to build a new version. The minifier here is YUI; Closure/UglifyJS are more aggressive, and
for some reason throw ridiculous issues in the Wii's browser that I've been unable to track down (and I don't have
more time to throw at it).

In short, the builds require Python/Java, but once you've got them all installed you should only need the command above.


Licensing, etc
-------------------------------------------------------------------------------------------------------------------
wii-js is released under an MIT license. Just provide credit where need be if you choose to use this, it's taken quite
a bit of my time to decipher the utterly random pieces and intricacies of this Javascript engine. ;)
