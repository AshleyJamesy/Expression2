# Tutorial 1: Elements (Basics)
## Create Elements:

```
@name Example
#include "library/egp/init.txt"

if(first() || dupefinished()) {}

if(egpEventClk("document.ready")) {
	#add elements to body here
}
```
**IMPORTANT:** All egp library functions should be called within some egpEventClk or an extension of it. 
If you're calling these functions outside an egpEvent, you may face concurrency issues.

#### Box:
```
if(egpEventClk("document.ready")) {
	egpBody():egpAdd(table(
		"type" = "box",
		"position" = vec2(),
		"size" = vec2(512,512),
		"colour" = vec4(255, 255, 255, 255),
		"outline" = 0, #box with no outline
		"rounded" = 0 #box with no rounded edges
	))
}
```
#### Circle:
```
if(egpEventClk("document.ready")) {
	egpBody():egpAdd(table(
		"type" = "circle",
		"position" = vec2(256, 256),
		"size" = vec2(256,256),
		"colour" = vec4(255, 255, 255, 255),
		"outline" = 0, #circle with no outline
	))
}
```
#### Text:
```
if(egpEventClk("document.ready")) {
	egpBody():egpAdd(table(
		"type" = "text",
		"position" = vec2(),
		"size" = vec2(512,512),
		"colour" = vec4(255, 255, 255, 255)
		"text" = "Hello, world",
		"align" = vec2(1, 1),
		"font" = table(
			"font" = "ChatFont",
			"size" = 18
		)
	))
}
```

## Create Children Elements:
Nested children can be easily created for egp objects that can have children (such as box)
```
if(egpEventClk("document.ready")) {
	egpBody():egpAdd(table(
		"type" = "box",
		"position" = vec2(),
		"size" = vec2(512,512),
		"colour" = vec4(255, 255, 255, 255),
		"outline" = 0, #box with no outline
		"rounded" = 0 #box with no rounded edges,
		"children" = table(
			table(
				"type" = "box",
				"position" = vec2(),
				"size" = vec2(256,256),
				"colour" = vec4(255, 0, 0, 255),
				"children" = table(
					table(
						"type" = "box",
						"position" = vec2(),
						"size" = vec2(128,128),
						"colour" = vec4(0, 255, 0, 255)
					)
				)
			)
		)
	))
	
	#add another child to the document body
	egpBody():egpAdd(table(
		"type" = "box",
		"position" = vec2(),
		"size" = vec2(512,512),
		"colour" = vec4(255, 255, 255, 255),
		"outline" = 0, #box with no outline
		"rounded" = 0 #box with no rounded edges
	))
}
```

## Position Elements:
Elements can be positioned using a `vec2(x, y)` or `table("x" = <n, s>, "y" = <n, s>)`
### Examples:
Position object at `0,0` pixels:
```
"position" = vec2(0, 0)
```
Position object at 50% of parent width and height
```
"position" = table(
	"x" = "50%",
	"y" = "50%"
)
```
Mixture of both examples:
```
"position" = table(
	"x" = "50%",
	"y" = 0
)
```
## Size Elements:
Elements can be sized using a `vec2(x, y)` or `table("x" = <n, s>, "y" = <n, s>)`
### Examples:
Size object `256,256` pixels:
```
"size" = vec2(256, 256)
```
Size the object to 100% of parent width and height
```
"size" = table(
	"width" = "100%",
	"height" = "100%"
)
```
Mixture of both examples:
```
"size" = table(
	"width" = "100%",
	"height" = 32
)
```
We can also size the font of text elements so the font is always the correct size:
```
table(
	"type" = "text",
	"position" = vec2(),
	"size" = "size" = table(
		"width" = "100%",
		"height" = 32
	),
	"colour" = vec4(255, 255, 255, 255)
	"text" = "Hello, world",
	"align" = vec2(1, 1),
	"font" = table(
		"font" = "ChatFont",
		"size" = "100%" #100% of the height of this element (32 pixels)
	)
)
```
## Anchor Elements
Anchoring changes the origin of the element (by default it is top left `0,0`)
Elements can be anchored using a `vec2(x, y)` or `table("x" = <n, s>, "y" = <n, s>)`
### Examples:
Anchor object `25,25` pixels:
```
"anchor" = vec2(32, 32)
```
Anchor the object to 50% of it's width and height (sets the origin to the center of the object)
```
"anchor" = table(
	"x" = "50%",
	"y" = "50%"
)
```
Mixture of both examples: Object's origin is bottom right
```
"anchor" = table(
	"x" = "100%",
	"y" = "100%"
)
```
# Tutorial 2. Element Events
It's important to note that whenever we use `Element:egpAdd(table(...))` that the objects are not created immediately  (as to not exceed tick quota). These function actually queue these objects to be added to the body/parent element.  
  
We will go into more detail regarding the event and queuing systems later in the tutorial.

For now, if we need to access an element & it's children immediately after it's drawn, we'll need to use an event callback:
(The callbacks are processed in order they were called)
```
#callback for when body element is ready
if(egpEventClk("document.ready")) {
	egpBody():egpAdd(table(
		"type" = "box",
		"colour" = vec4(255, 255, 255, 255),
		"events" = table(
			"loaded" = "mybox1.ready" #can replace this with any event name
		)
	))
	
	#add another child to body
	egpBody():egpAdd(table(
		"type" = "box",
		"size" = table(
			"width" = "50%",
			"height" = "50%"
		),
		"colour" = vec4(0, 255, 0, 255),
		"events" = table(
			"loaded" = "mybox2.ready" #give it a callback
		)
	))
}

#this is called once the element and all it's children are drawn
if(egpEventClk("mybox1.ready")) {
	print("mybox1.ready")
	local Event = egpEventData()
	Event["target", table]:egpColour(vec4(255, 0, 0, 255)) #change the colour of this element to red
	
}

#this callback is processed 2nd
if(egpEventClk("mybox2.ready")) {
	print("mybox2.ready")
	local Event = egpEventData()
	Event["target", table]:egpColour(vec4(0, 0, 255, 255)) #change the colour of this element to green
}
```

## Removing Elements
To remove an element it first needs to created on screen, hence why we focused on basic events in the previous section.
Just as adding elements were not immediately drawn, the same occurs for removing elements.

We can also setup a remove callback if we need to do anything when the element is removed.

### Examples:
```
#called when body element is ready
if(egpEventClk("document.ready")) {
	egpBody():egpAdd(table(
		"type" = "box",
		"colour" = vec4(255, 255, 255, 255),
		"events" = table(
			"loaded" = "mybox.ready" #can replace this with any event name
			"removed" = "mybox.removed" #can replace this with any event name
		)
	))
}

#this is called once the element and all it's children are drawn
if(egpEventClk("mybox.ready")) {
	local Event = egpEventData()
	Event["target", table]:egpRemove() #remove the element
}

#this is called once the element and all it's children are removed
if(egpEventClk("mybox.removed")) {
	local Event = egpEventData()
	#cleanup something
}
```

# Tutorial 3. Elements (Advanced)
Now we know how to add & remove elements, lets take a look at some extra functions we can use on objects rendered to the egp.

## Children
As you've seen from previous sections we can create children in our objects, here are a few methods to get those children.

`Element:egpChildren()` - table of children in index order
`Element:egpChild(Index:number)` - gets child by index
`Element:egpChild(Name:string)` - gets child by name (see below)
`egpGet(Path:string)` - gets child by path (see below)

## Naming & Pathing
We can actually name our elements and children so we can get them easily anywhere in our code.
We can use two functions to get these objects.

`egpGet(Path:string)` - will get objects starting at body. eg: `egpGet(body.child1.nestedchild1)`
`Element:egpChild(Name:string)`

### Example:
```
#callback for when body element is ready
if(egpEventClk("document.ready")) {
	egpBody():egpAdd(table(
		"name" = "mybox1",
		"type" = "box",
		"colour" = vec4(255, 255, 255, 255),
		"events" = table(
			"loaded" = "mybox1.ready" #can replace this with any event name
		),
		"children" = table(
			table(
				"name" = "items",
				"type" = "box",
				"size" = table(
					"width" = "50%",
					"height" = "50%"
				),
				"colour" = vec4(0, 255, 0, 255)
			)
		)
	))
}

#this is called once the element and all it's children are drawn
if(egpEventClk("mybox.ready")) {
	local Event = egpEventData()
	local Element = Event["target", table] #the mybox element
	Element = egpBody():egpChild("mybox1") #the same mybox element
	Element = egpGet("body.mybox1") #the same mybox element
	
	local ElementChild = egpGet("body.mybox1.items") #get the child of mybox1
}
```
## Positioning
TODO: add readme documentation for Element:egpPos(...)
## Sizing
TODO: add readme documentation for Element:egpSize(...)
## Colouring
TODO: add readme documentation for Element:egpColour(...)
## Text
TODO: add readme documentation for Element:egpText(...)
# Tutorial 4. Events:
### Description:
To ensure we're not interrupting the render queue, all triggers or draws (add, remove, redraw) must be processed through an egpEvent. Calling any egp functions (some exceptions) outside this process may cause errors.

The process is as follows:  `Input events update State > State updates render > Render queue is processed.`

You've already seen the simpliest version of this above with `egpEventClk("document.ready")`

## Event Examples:
Add a child to "mybox" once "mybox" is created
```
#called when body element is ready
if(egpEventClk("document.ready")) {
	egpBody():egpAdd(table(
		"type" = "box",
		"colour" = vec4(255, 255, 255, 255),
		"events" = table(
			"loaded" = "mybox.ready" #can replace this with any event name
			"removed" = "mybox.removed" #can replace this with any event name
		)
	))
}

#add another child to mybox
if(egpEventClk("mybox.ready")) {
	local Event = egpEventData()
	Event["target", table]:egpAdd(table(
		"type" = "box",
		"size" = table(
			"width" = "50%",
			"height" = "50%"
		),
		"colour" = vec4(0, 255, 0, 255)
	))
}
```
## Pushing Events
Just as the loaded and removed events can be called, we can push custom events to separate our logic.
### Example:
```
#called when body element is ready
if(egpEventClk("document.ready")) {
	egpBody():egpAdd(table(
		"type" = "box",
		"colour" = vec4(255, 255, 255, 255),
		"events" = table(
			"loaded" = "mybox.ready"
		)
	))
	egpEvent("done", table(
		"data" = "hello, world"
	))
}

#called first
if(egpEventClk("mybox.ready")) {
	print("loaded")
}

#called second
if(egpEventClk("done")) {
	local Event = egpEventData()
	print(Event["data", string])
	
	egpBody():egpChild(1):egpAdd(table(
		"type" = "box",
		"size" = table(
			"width" = "50%",
			"height" = "50%"
		),
		"colour" = vec4(0, 255, 0, 255)
	))
}
```

## Pushing Events (Delayed)
Just like a timer we can push an event to be processed later, useful for checks which need time in-between.
```
egpEventDelay(Name:string, Data:table, Delay:number) #with data
egpEventDelay(Name:string, Delay:number) #no data
```
### Example:
```
#called when body element is ready
if(egpEventClk("document.ready")) {
	egpBody():egpAdd(table(
		"type" = "box",
		"colour" = vec4(255, 255, 255, 255),
		"events" = table(
			"loaded" = "mybox.ready"
		)
	))

	#push event in 5sec
	egpEventDelay("done", table(
		"data" = "hello, world"
	), 5000)
}

#called after 5 seconds
if(egpEventClk("done")) {
	local Event = egpEventData()
	print(Event["data", string])
	
	egpBody():egpChild(1):egpAdd(table(
		"type" = "box",
		"size" = table(
			"width" = "50%",
			"height" = "50%"
		),
		"colour" = vec4(0, 255, 0, 255)
	))
}
```

## Input Events:
There are some inbuilt input events in the library:
This include mouse and keyboard events
```
#called when a user presses the screen (works for screen, emitter and hud)
if(egpEventClk("mouse.press")) {
	local Event = egpEventData()
	local User = Event["user", entity] #user who pressed screen
	local Cursor = Event["cursor", vector2] #where the cursor was when screen was pressed
}

#called when a user presses key in keyboard
#this will also be called if the user is holding the key down
if(egpEventClk("keyboard.press")) {
	local Event = egpEventData()
	local User = Event["user", entity] #user who pressed key
	local Cursor = Event["cursor", vector2] #where the cursor was when key was pressed
	local Key = Event["key", number] #key pressed for this event
	local Keys = Event["keys", array] #array of keys being pressed
}

#called when a user enters the keyboard
if(egpEventClk("keyboard.enter")) {
	local Event = egpEventData()
	local User = Event["user", entity] #user who entered keyboard
}

#called when a user exits the keyboard
if(egpEventClk("keyboard.exit")) {}
```

## Custom Input Events:
As stated in previous sections, any egp functions called should be within an egpEventClk

Custom Input events are used when you want other inputs / triggers to interact with the egp library.
This could be an Input or E2 Event for example

It's easy to setup a custom input event using 
```
egpInputEvent(Name:string, Data:Table) #with data
egpInputEvent(Name:string) #no data
```

Lets take a look at a chat example:
```
if(egpEventClk("document.ready")) {
	egpBody():egpAdd(table(
		"name" = "message",
		"type" = "text",
		"position" = vec2(),
		"size" = vec2(512,512),
		"colour" = vec4(255, 255, 255, 255)
		"text" = "Hello, world",
		"align" = vec2(1, 1),
		"font" = table(
			"font" = "ChatFont",
			"size" = 18
		)
	))
}

#the trigger event
event chat(Player:entity, Message:string, Team:number) {
	#push the event to be processed by the egp library
	egpInputEvent("chat", table(
		"player" = Player,
		"message" = Message
	))
}

#process the egp event
if(egpEventClk("chat")) {
	local Event = egpEventData()
	local Player = Event["player", entity]
	local Message = Event["message", string]
	egpBody():egpChild("message"):egpText(Player:name() + ": "+ Message) #update the message object
}
```
