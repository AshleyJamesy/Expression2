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
It's important to note that the element wont be created immediately, this is because it is queued to be drawn later as to not exceed tick quota.
Below is an example to create a red background element using the `color` property.  
```lua
@name Example
#include "library/egp"

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
## Types
The available types are: `box`, `circle`, `wedge` and `text`  
Elements:  
```lua
string Element:egpType() --gets the type of the element
numner Element:egpId() --gets the egpid of the element
```
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
Elements:  
```lua
vec2 Element:egpPos() --returns the current local position in px
vec2 Element:egpGlobalPos() --returns the current global position in px
Element:egpPos(Position:vector2)
Element:egpPos(Position:table)
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
Elements:  
```lua
vec2 Element:egpAnchor() --returns the current anchor in px
Element:egpAnchor(Anchor:vector2)
Element:egpAnchor(Anchor:table)
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
Elements:  
```lua
vec2 Element:egpSize() --returns the current size in px
Element:egpSize(Size:vector2)
Element:egpSize(Size:table)
```
## Color Elements
Elements can be colored using `vec(r,g,b)`, `vec4(r,g,b,a)`, `"#FFFFFF"`
### Examples:
Colors the element white
```lua
"color" = vec4(255,255,255,255)
```
Colors the element red
```lua
"color" = vec(255,0,0)
```
Colors the element blue
```lua
"color" = "#0000FF"
```
Elements:  
```lua
vec4 Element:egpColor() --returns the current color
Element:egpColor(Hex:string)
Element:egpColor(Color:vector)
Element:egpColor(Color:vector4)
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
Elements:  
```lua
number Element:egpOutline() #returns the current thickness in px
Element:egpOutline(Thickness:number)
```
## Rounded Elements
Box elements can be outlined using `number:thickness`
### Examples:
Makes the box rounded
```lua
"rounded" = 1
```
## Materials
Elements can have materials such as icons or background images
### Examples:
```lua
"material" = "path/to/material"
```
Elements:  
```lua
string Element:egpMaterial() --returns the current material
Element:egpMaterial(Material:string)
```
## Text and Fonts
Elements can have text and fonts properties (only available on `text` type objects)
### Examples:
```lua
"text" = "Hello, world"
```
Align the text (center horizontally and verticall)
```lua
"align" = vec2(1,1)
```
Font and Size
Will use whatever the default font is, set by: `egpDefaultFont`
```lua
"font" = table(
	"font" = "",
	"size" = 18
)
```
The font size will be 100% height of the text object.
```lua
"font" = table(
	"font" = "Arial",
	"size" = "100%"
)
```
Set the default font:
```lua
egpDefaultFont(Font:string) --default font to use when font = ""
```
Elements:  
```lua
string Element:egpText() --returns the current text
Element:egpText(Text:string)
number Element:egpFontSize() --returns the current font size in px
Element:egpFont(Font:string, Size:number)
Element:egpFont(Font:string, Size:string)
vec2 Element:egpAlign() --returns the current alignment
Element:egpAlign(Align:vec2)
```
## Nested Children
Nested children can be created by adding a `children` table to your properties.
Below is an extended version of the example we  had above that creates a centred green box
```lua
@name Example
#include "library/egp"

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
table Element:egpParent() --gets the parent element (throws error if no parent)
number Element:egpHasParent() --gets if child hsa a parent
table Element:egpChildren() --gets all children on element
number Element:egpChildExists(Index:number) --gets if child exists by index
number Element:egpChildExists(Name:string) --gets if child exists by name
table Element:egpChild(Index:number) --gets child element by index
table Element:egpChild(Name:string) --gets child element by name
table Element:egpChildFirst() --gets first child element (throws error if no children)
table Element:egpChildLast() --gets first child element (throws error if no children)
number Element:egpIndex() --gets the index of the element
string Element:egpPath() --gets the path of the element
string Element:egpName() --gets the name of the element
table egpGet(Path:string) --gets element by path eg: `document.background`
```
## Paths & Names
You can assign your elements names, making them quick to find and manipulate.
```lua

```
```lua
@name Example
#include "library/egp"

if(egpUserChanged()) {
    egpSetUser(egpUser())
}

if(egpWirelinkChanged()) {
    egpClear(512,512,function(Event:table, _:function) {
        Event["target", table]:egpAdd(table(
            "name" = "child1",
            "type" = "box",
            "color" = vec4(255, 0, 0, 255),
            "children" = table(
                table(
                    "name" = "child2",
                    "type" = "box",
                    "anchor" = table("x" = "50%", "y" = "50%"),
                    "position" = table("x" = "50%", "y" = "50%"),
                    "size" = table("width" = "25%", "height" = "25%"),
                    "color" = vec4(0, 255, 0, 255)
                )
            )
        ))
        
        egpCallback(function(_:table, _:function) {
            local Child1 = egpGet("document.child1")
            local Child1 = egpDocument():egpChild("child1")
            print(Child1:egpName())
            
            local Child2 = egpGet("document.child1.child2")
            local Child2 = egpDocument():egpChild("child1"):egpChild("child2")
            print(Child2:egpName())
        })
    })
}
```
## Removing Elements
Similar with creating elements, the element is not removed immediately because all function calls are queued to be called later.
```lua
Element:egpRemove()
Element:egpRemoveChildren()
```
There are a number of ways to trigger events after the element is removed though which will be discussed in the next section.
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
#include "library/egp"

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
#include "library/egp"

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
## Callbacks: Advanced
The whole library is built on the idea that we queue logic to be run later, this is what an egpCallback acheives.  
Internally behind the scenes these functions are called for everything this library does.  
  
In previous sections you saw most egp functions are queued, this includes functions like egpAdd, egpRemove and egpEvent.  
This means the tick you call an egp function on will not necessarily update the DOM immediately.  
  
This can get confusing quickly, so to overcome this we can also use these egpCallbacks to queue logic to run later.
```lua
egpCallback(Callback:function, Data:table, Index:number, Clk:number)
egpCallback(Callback:function, Data:table, Index:number)
egpCallback(Callback:function, Data:table)
egpCallback(Callback:function)
```
Callback Function:  
The callback parameters include the Data from the caller and the Callback function (so you don't need to redeclare the callback function to reusue it)  
```lua
function(Data:table, Callback:function) {

}
```
| Parameter | Comments |
|--|--|
| Callback:function | The callback function to run |
| Data:table | Data to pass to the callback function |
| Index:number | Where in the queue to add this callback |
| Clk:number | This callback should be processed on a new tick (useful when the callback does something cpu intensive) |  
  
Here is an example:
```lua
@name Example
#include "library/egp"

if(egpUserChanged()) {
    egpSetUser(egpUser())
}

if(egpWirelinkChanged()) {
    egpClear(512,512,function(Event:table, _:function) {
        Event["target", table]:egpAdd(table(
            "name" = "child1",
            "type" = "box",
            "color" = vec4(255, 0, 0, 255),
            "children" = table(
                table(
                    "name" = "child2",
                    "type" = "box",
                    "anchor" = table("x" = "50%", "y" = "50%"),
                    "position" = table("x" = "50%", "y" = "50%"),
                    "size" = table("width" = "25%", "height" = "25%"),
                    "color" = vec4(0, 255, 0, 255)
                )
            )
        ))
    })
    
    #this will error because child1 is not created immediately
    #local Child1 = egpGet("document.child1")
    
    #this will work because this is queued to run after egpClear and egpAdd
    egpCallback(function(Data:table, _:function) {
        local Child1 = egpGet("document.child1.child2")
        print(Child1:egpName())
	print(Data["message", string])
    }, table("message" = "Hello, world"))
}
```
## Lists
The library contains functions to create various lists (horizontal and vertical)
```lua
Element:egpList(List:table, Start:number, Max:number, Spacing:number, Vertical:number, Hide:number, Callback:function)
Element:egpList(List:table, Start:number, Max:number, Spacing:number, Callback:function)
Element:egpScrollableList(List:table, Max:number, Spacing:number, Style:table, Hide:number, Callback:function)
Element:egpGrid(Rows:table, Spacing:number, Hide:number, Callback:function)
```
| Parameter | Comments |
|--|--|
| List | A list of tables for each row |
| Rows | A list of tables of tables for each cell |
| Start | Which index does the list start at (useful for scrolling lists) |
| Max | Number of items to show |
| Spacing | The space in px between each list item |
| Style | A table of colors to style the |
| Vertical | Whether or not it's vertical or horizontal |
| Hide | Hide the list items while it's being drawn |
| Callback | A callback to decide how to draw each list item |

It's pretty easy to call these functions, the callback function has two parts you either update an existing element and return early, or you return a new table to be created.
```lua
This:egpList(List, 1, 5, 8, function(Event:table, _:function) {
	local ElementTarget = Event["target", table]
	
	local ListItem = Event["item", table]
	local Index = Event["index", number]
	local Position = Event["position", table]
	local Size = Event["size", table]
	
	#list item is already created, lets update the label for it
	if(ElementTarget != egpNoElement()) {
		#do updates in here
		return egpNoElement() #return noelement
	}
	
	#return element to be created
	return table(
		"position" = Position,
		"size" = Size
	)
})
```
Here is an actual example:  
```lua
@name Example
#include "library/egp"

if(egpUserChanged()) {
    egpSetUser(egpUser())
}

function void table:customList(List:table) {
    This:egpList(List, 1, 5, 8, function(Event:table, _:function) {
        local ElementTarget = Event["target", table]
        
        local ListItem = Event["item", table]
        local Index = Event["index", number]
        local Position = Event["position", table]
        local Size = Event["size", table]
        
        local Label = ListItem["value", string]
        
        if(ElementTarget != egpNoElement()) {
            ElementTarget:egpChild("label"):egpText(Label)
            return egpNoElement()
        }
        
        return table(
            "position" = Position,
            "size" = Size,
            "color" = "#0000FF",
            "children" = table(
                table(
                    "name" = "label",
                    "type" = "text",
                    "anchor" = table("x" = "0%", "y" = "0%"),
                    "position" = table("x" = "0%", "y" = "0%"),
                    "size" = table("width" = "100%", "height" = "100%"),
                    "color" = "#FFFFFF",
                    "text" = Label,
                    "align" = vec2(1,1),
                    "mouse-events" = "none"
                )
            )
        )
    })
}

if(egpWirelinkChanged()) {
    egpClear(512,512,function(Event:table, _:function) {
        Event["target", table]:egpAdd(table(
            "name" = "main",
            "type" = "box",
            "color" = vec4(255, 0, 0, 255),
            "children" = table(),
            "events" = table(
                "added" = function(Event:table, _:function) {
                    local Target = Event["target", table]
                    Target:customList(table(
                        table("value" = "A"),
                        table("value" = "B"),
                        table("value" = "C"),
                        table("value" = "D"),
                        table("value" = "E"),
                        table("value" = "F"),
                        table("value" = "G"),
                        table("value" = "H")
                    ))
                }
            )
        ))
    })
}
```
We can make it scrollable instead:  
```lua
@name Example
#include "library/egp"

if(egpUserChanged()) {
    egpSetUser(egpUser())
}

function void table:customList(List:table) {
    local Style = table(
        "background" = "#191919",
        "button" = table(
            "active" = "#0000FF",
            "disabled" = "#2d2d2d"
        ),
        "rounded" = 1
    )
    
    This:egpScrollableList(List, 5, 8, Style, 0, function(Event:table, _:function) {
        local ElementTarget = Event["target", table]
        
        local ListItem = Event["item", table]
        local Index = Event["index", number]
        local Position = Event["position", table]
        local Size = Event["size", table]
        
        local Label = ListItem["value", string]
        
        #the list item is already created, lets update the label for it
        if(ElementTarget != egpNoElement()) {
            ElementTarget:egpChild("label"):egpText(Label)
            return egpNoElement()
        }
        
        #the list item hasn't been created yet, return element to be created
        return table(
            "position" = Position,
            "size" = Size,
            "color" = "#0000FF",
            "children" = table(
                table(
                    "name" = "label",
                    "type" = "text",
                    "anchor" = table("x" = "0%", "y" = "0%"),
                    "position" = table("x" = "0%", "y" = "0%"),
                    "size" = table("width" = "100%", "height" = "100%"),
                    "color" = "#FFFFFF",
                    "text" = Label,
                    "align" = vec2(1,1),
                    "mouse-events" = "none"
                )
            )
        )
    })
}

if(egpWirelinkChanged()) {
    egpClear(512,512,function(Event:table, _:function) {
        Event["target", table]:egpAdd(table(
            "name" = "main",
            "type" = "box",
            "color" = vec4(255, 0, 0, 255),
            "children" = table(),
            "events" = table(
                "added" = function(Event:table, _:function) {
                    local Target = Event["target", table]
                    Target:customList(table(
                        table("value" = "A"),
                        table("value" = "B"),
                        table("value" = "C"),
                        table("value" = "D"),
                        table("value" = "E"),
                        table("value" = "F"),
                        table("value" = "G"),
                        table("value" = "H")
                    ))
                }
            )
        ))
    })
}
```
If you peek at the `egpScrollableList` function in the library, you'll see it's just a wrapper for `egpList`