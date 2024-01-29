# EGP - Quick Start Guide

## Initial Setup
To begin we need to setup some starting code, below is the example.
```lua
@name Example
#include "library/egp"

if(egpWirelinkChanged()) {
	egpClear(512,512,function(Event:table, _:function) {
		
	})
}
```
Here we call `egpClear` with size` 512x512` (you can use a different size if using egp_hud) and a callback function.

The callback definition takes two parameters, an event table and callback function, for now we will focus on the event table.
The event table will contain useful properties we can use such as `target` (similar to javascript)

The `target` property is always an element. In this case it's referring to the `document` element.
Lets see how we can use this `document` element in the next section.

## Creating Elements
Create elements with `table:egpAdd(Properties:table)` - This function should only be called on an existing element.
It's important to note that the element wont be created right away, this is because it is queued to be drawn later as to not exceed tick quota.
Below is an example to create a red background element using the `color` property.
```lua
@name Example
#include "library/egp/8.0/init"

if(egpWirelinkChanged()) {
	egpClear(512,512,function(Event:table, _:function) {
		Event["target", table]:egpAdd(table(
			"type" = "box",
			"color" = vec4(255, 0, 0, 255)
		))
	})
}
```
Lets take a look at some other properties we can use.
## Position Elements
Elements can be positioned using a `vec2(x, y)` or `table("x" = <n, s>, "y" = <n, s>)`
### Examples:
Position object at  `0,0`  pixels:
```lua
"position" = vec2(0, 0)
```

Position object at 50% of parent width and height
```lua
"position" = table(
	"x" = "50%",
	"y" = "50%"
)
```

Mixture of both examples:
```lua
"position" = table(
	"x" = "50%",
	"y" = 0
)
```
## Size Elements
Elements can be sized using a  `vec2(x, y)`  or  `table("x" = <n, s>, "y" = <n, s>)`
### Examples:
Size object  `256,256`  pixels:
```lua
"size" = vec2(256, 256)
```
Size the object to 100% of parent width and height
```lua
"size" = table(
	"width" = "100%",
	"height" = "100%"
)
```
Mixture of both examples:
```lua
"size" = table(
	"width" = "100%",
	"height" = 32
)
```
## Anchor Elements
Elements can be anchored using a  `vec2(x, y)`  or  `table("x" = <n, s>, "y" = <n, s>)`
Anchoring changes the origin of the element (default is top left  `0,0`)
### Examples:
Anchor object  `25,25`  pixels:
```lua
"anchor" = vec2(32, 32)
```
Anchor the object to 50% of it's width and height (sets the origin to the center of the object)
```lua
"anchor" = table(
	"x" = "50%",
	"y" = "50%"
)
```
Mixture of both examples: Object's origin is bottom right
```lua
"anchor" = table(
	"x" = "100%",
	"y" = "100%"
)
```
## Outline Elements
Box & Circle elements can be outlined using `number:thickness`
### Examples:
Outlines the element with thickness of `1px`
```lua
"outline" = 1
```
Outlines the element with thickness of `3px`
```lua
"outline" = 3
```
## Color Elements
Elements can be colored using `vec4(r,g,b,a)`
### Examples:
Colors the element white
```lua
"color" = vec4(255,255,255,255)
```
Outlines the element red
```lua
"color" = vec4(255,0,0,255)
```
## Materials
Elements can have materials such as icons or background images
### Examples:
```lua
"material" = "path/to/material"
```
## Nested Children
Nested children can be created by adding a `children` table to your properties.
Below is an extended version of the example we  had above that creates a centred green box
```lua
@name Example
#include "library/egp/8.0/init"

if(egpWirelinkChanged()) {
	egpClear(512,512,function(Event:table, _:function) {
		Event["target", table]:egpAdd(table(
			"type" = "box",
			"color" = vec4(255, 0, 0, 255),
			"children" = table(
				table(
					"type" = "box",
					"anchor" = table("x" = "50%", "y" = "50%"),
					"position" = table("x" = "50%", "y" = "50%"),
					"size" = table("width" = "25%", "height" = "25%"),
					"color" = vec4(0, 255, 0, 255)
				)
			)
		))
	})
}
```
## Parent & Children Elements
As you've seen from previous sections we can create children in our elements, here are a few methods we can use on our elements.
```lua
Element:egpParent() --gets the parent element (throws error if no parent)
Element:egpChildren() --gets all children on element
Element:egpChildExists(Index:number) --gets if child exists by index
Element:egpChildExists(Name:string) --gets if child exists by name
Element:egpChild(Index:number) --gets child element by index
Element:egpChild(Name:string) --gets child element by name
Element:egpChildFirst() --gets first child element (throws error if no children)
Element:egpChildLast() --gets first child element (throws error if no children)
egpGet(Path:string) --gets element by path eg: `document.background`
```
## Removing Elements
```lua
Element:egpRemove()
```
## Paths & Names


## Element Events
There are various events which come with the library to assist you in building UIs, see below for all available events.
Similar to the `callback` used in `egpClear`,  the function definition is the same `function(Event:table, Callback:function)`
| Event | Triggered | Bubbles | Requires |
|--|--|--|--|
| added | Called when element is added | No ||
| removed | Called when element is removed | No ||
| mousedown | Called when element is pressed | Yes ||
| mouseenter | Called when mouse enters element | No | egpSetUser(Player:entity) |
| mouseexit | Called when mouse exits element | No | egpSetUser(Player:entity) |
| mouseover | Called while mouse is over element | Yes | egpSetUser(Player:entity) |

**Bubbling** means the event is also called in all parent elements

To avoid using a lot of ops, we need to set which `Player` will trigger the `mouseenter`, `mouseexit` and `mouseover` events. In our case we can set it to whoever last pressed the screen using the following code:
```lua
if(egpUserChanged()) {
    egpSetUser(egpUser())
}
```

Lets see an example of all these events in action:
```lua
@name Example
#include "library/egp/8.0/init"

if(egpUserChanged()) {
    egpSetUser(egpUser())
}

if(egpWirelinkChanged()) {
	egpClear(512,512,function(Event:table, _:function) {
		Event["target", table]:egpAdd(table(
			"type" = "box",
			"color" = vec4(255, 0, 0, 255),
			"children" = table(
				table(
					"type" = "box",
					"anchor" = table("x" = "50%", "y" = "50%"),
					"position" = table("x" = "50%", "y" = "50%"),
					"size" = table("width" = "25%", "height" = "25%"),
					"color" = vec4(0, 255, 0, 255),
					"events" = table(
						"added" = function(_:table, _:function) { print("green box added!") },
						"removed" = function(_:table, _:function) { print("green box removed!") },
						"mousedown" = function(_:table, _:function) { print("green box pressed!") },
						"mouseenter" = function(_:table, _:function) { print("green box mouseenter!") },
						"mouseexit" = function(_:table, _:function) { print("green box mouseexit!") },
						"mouseover" = function(_:table, _:function) { print("green box mouseover!") }
					)
				)
			)
		))
	})
}
```
You can use an array to call multiple functions instead of just one:
```lua
"events" = table(
	"mousedown" = array(
		function(Event:table, _:function) { print("green box pressed 1!") },
		function(Event:table, _:function) { print("green box pressed 2!") }
	)
)
```
You can also create custom events and call any event using the `Element:egpEvent(Event:string, Data:table, Bubble:number)`
eg:
```lua
Event["target", table]:egpEvent("mousedown", table("target" = Event["target", table]), 1)
```
### Examples:
Lets modify the code to make it so while the mouse is over the green box element, it changes color and when the element is pressed it is removed. **Note:** Remember to press on the screen so the hover events are checked for you.
```lua
@name Example
#include "library/egp/8.0/init"

if(egpUserChanged()) {
    egpSetUser(egpUser())
}

if(egpWirelinkChanged()) {
	egpClear(512,512,function(Event:table, _:function) {
		Event["target", table]:egpAdd(table(
			"type" = "box",
			"color" = vec4(255, 0, 0, 255),
			"children" = table(
				table(
					"type" = "box",
					"anchor" = table("x" = "50%", "y" = "50%"),
					"position" = table("x" = "50%", "y" = "50%"),
					"size" = table("width" = "25%", "height" = "25%"),
					"color" = vec4(0, 255, 0, 255),
					"events" = table(
						"mousedown" = function(Event:table, _:function) { Event["target", table]:egpRemove() },
						"mouseover" = function(Event:table, _:function) { Event["target", table]:egpColor(randvec() * 255) }
					)
				)
			)
		))
	})
}
```
## Lists
The library contains functions to create lists (horizontal and vertical)
```lua
Element:egpList(List:table, Start:number, Max:number, Spacing:number, Vertical:number, Hide:number, Callback:function)
Element:egpList(List:table, Start:number, Max:number, Spacing:number, Callback:function)
Element:egpScrollableList(List:table, Max:number, Spacing:number, Style:table, Hide:number, Callback:function)
```
**TODO:** Add documentation and examples

## Callbacks: Advanced
**TODO:** Add documentation and examples
