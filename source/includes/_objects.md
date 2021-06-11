# Objects

ChatTriggers provides several objects to expand the functionality of your imports without you needing to delve into base
Minecraft code. A list is found below.

<aside class="warning">It is bad practice to create new object every time you use it, they're heavy objects.
Create one in the global scope, and refer back to it.</aside>

| Object                  | Description                                                            |
| ----------------------- | ---------------------------------------------------------------------- |
| [Book](#books)          | Makes an openable book in Minecraft                                    |
| [CPS](#cps)             | Contains information about the player's clicks per second              |
| [Display](#displays)    | Renders text on to the game screen                                     |
| [Gui](#guis)            | Makes an openable gui in Minecraft                                     |
| [Inventory](#inventory) | Contains information about the player's inventory                      |
| [KeyBind](#keybinds)    | Used for detecting a key's state                                       |
| ParticleEffect          | Allows creation of custom particle effects to be displayed client side |
| [Player](#player)       | Used for getting information about the player                          |
| Thread                  | This is a pseudo object, used to do tasks that take a long time        |

# Books

`Book` objects are used for displaying base Minecraft book GUIs with customizable text.

## Creation

> This is how you create a book

```js
const book = new Book("Example Book");
```

We create our book with the Book constructor of `new Book(bookName);`. We want to create our book in the global scope,
as explained below.

## Adding content

> This is how you add pages to the book

```js
const book = new Book("Example Book");

book.addPage("This is a very simple page with just text.");
book.addPage(new Message("This is a page with a ", new TextComponent("twist!").setHoverValue("Hi! I'm hover text :o")));
```

To add content to our book, we'll want to utilize the `.addPage(message)` method. This can take either a simple
string as the message for the page, or a `Message` object if you want to utilize the functionality the provide, covered
[here](#message-objects). This should be done right after the instantiation of the book.

## Updating content

> This is how to update a page's content

```js
book.setPage(1, new Message("lol!"));
```

To set a page, we use the `.setPage(pageNumber, message)`. Page number is the number of the page you wish to update,
0 based. The message _has_ to be a `Message` object, there is no method for just a string.

This can be done anytime, just re-display the book to see the updated version. The page you try to set must already exist,
or else there will be errors. Just add the page if you need to add a new page afterwards.

## Displaying

> This is how to display the book to the user

```js
book.display();
```

> This is how to display the book starting on a certain page

```js
book.display(1);
```

This is a very simple operation which just opens the book. You can also specify a page number to open the book to as
the first argument, it defaults to 0. If the page you specify doesn't exist, the player will have to click one of the
arrows to go to the next available page.

# CPS

> The `CPS` object gives information about the player's clicks per second.

## Clicks per second

> To get the left clicks per second, use this

```js
const leftClicks = CPS.getLeftClicks();
```

> To get the right clicks per second, use this

```js
const rightClicks = CPS.getRightClicks();
```

There are more methods for the `CPS` object, but these are the most common. You can always see a full list
of up to date documentation on the [JavaDocs](https://www.chattriggers.com/javadocs).

# Displays

Displays are used for rendering simple text on to the players screen. If you would like to utilize other rendering
functions found in [the rendering section](#rendering), use custom rendering functions.

## Creation

> This is how you can create a `Display` object

```js
const display = new Display();
```

This `Display` object is now created, but it doesn't do much of anything yet.

## Adding content

> This is how you can add lines to a display

```js
display.addLine("Ay! First line.");
display.addLines("2nd line", "3rd line");
display.addLines(2);
display.addLine(new DisplayLine("2 line gap above me!"));
```

`Displays` consist of lines of text. These lines can be added and set, and they can use color codes. The first call to
`.addLine(message)` adds a line to the display with the text passed in. The second call to `.addLines(messages...)` adds
as many lines as you pass into it, in our case just 2. If you don't pass in Strings or `DisplayLines`, then it will just add one
line per parameter you passed (e.g `.addLines(2)` will only add 1 empty line despite the number, but `.addLines(0, 0)`
will add 2 empty lines due to there being 2 parameters).

## Setting content

> This is how you set a line in a display

```js
display.setLine(3, "Now this line has text :)");
```

In this example the call to `.setLine(lineNumber, message)` sets the 4th line in the display (0 based) which was previously
blank to our example text. This is what we would use if we want to update a display with information, like the player's
current coordinates.

## Setting positioning

> This is how you set the alignment of a display

```js
display.setAlign("left");
```

This aligns your display on the left side of the screen. Other options are `"center"` and `"right"`.

> This is how you set the order of the lines. Both of these ways will work, setting the order to go down.

```js
display.setOrder("down");
display.setOrder(DisplayHandler.Order.DOWN);
```

This renders the lines from 0 going downwards, usually what you'd want. The other option is `"up"`.

> This is how you set the exact position of the display

```js
display.setRenderLoc(10, 10);
```

This sets the X and Y coordinate of where your display should start, with the first argument being X, and the second being Y.

## Setting background and foreground options

> This sets the background color of the display

```js
display.setBackgroundColor(Renderer.AQUA);
```

This makes the background color of the display aqua. Other options are all the colors in Renderer, or a custom color
with `Renderer.color(r, g, b, a)`.

> This sets the type of background for the display to be fit per line. Both of these options will work.

```js
display.setBackground(DisplayHandler.Background.PER_LINE);
display.setBackground("per line");
```

This option sets how the background color should be displayed. `PER_LINE` says that the background should be the width
of each line. `NONE` would mean don't show background color, and `FULL` would indicate make the background color draw in
a box around the entire display.

> This sets the foreground (text) color of the display

```js
display.setTextColor(Renderer.BLUE);
```

All text in the display will now show blue. This method can take any Renderer color, including custom ones described above.

DisplayLine can also be used to set the text color, background color, and alignment for a specific line.

```js
display.addLine(
  new DisplayLine("This will have green text!").setTextColor(Renderer.GREEN)
);
```

This will set the new line to have green text, overriding the blue we set earlier.

# Guis

Guis are screens that are opened in game, such as the chat gui, or the escape menu. These stop the player from moving.

## Creation

> This is how you create a gui

```js
const gui = new Gui();
```

Like other objects, creating a Gui is very simple.

## Rendering the gui

> This is how you set up a function to render the gui

```js
gui.registerDraw(myGuiRenderFunction);

function myGuiRenderFunction(mouseX, mouseY, partialTicks) {
  Renderer.drawRect(Renderer.WHITE, mouseX, mouseY, 50, 50);
}
```

Everything inside of the "myGuiRenderFunction" will be ran while the gui is open. Inside of this function you should
make use of the [Renderer](#rendering) functions to draw what you want on to the screen. The three arguments passed in
are the x coordinate of the user's mouse, the y coordinate of the users mouse, and the partial ticks.

In this example, we render a 50x50 square with the top left corner being the user's mouse position.

## Adding interactivity

> This is how you detect when a user presses a key

```js
gui.registerKeyTyped(myGuiKeyTypedFunction);

function myGuiKeyTypedFunction(typedChar, keyCode) {
  ChatLib.chat("You typed " + typedChar);
}
```

> This is how you detect when the user clicks

```js
gui.registerClicked(myGuiClickedFunction);
gui.registerDraw(myGuiRenderFunction);

let renderSquareX = 0;
let renderSquareY = 0;

function myGuiRenderFunction(mouseX, mouseY, partialTicks) {
  Renderer.drawRect(Renderer.WHITE, renderSquareX, renderSquareY, 50, 50);
}

function myGuiClickedFunction(mouseX, mouseY, button) {
  renderSquareX = mouseX;
  renderSquareY = mouseY;
}
```

In the first example we register our key typed function to be activated when someone types a key in our Gui. The keyCode
passed can be compared with the Keyboard class keys.

In the next example we make things more complicated. We register both a draw function and a mouse clicked function.
We render the same box as in the previous draw example, however, we make the coordinates of it be the last place the
mouse was clicked.

## Displaying the gui

> To display the gui, use this

```js
gui.open();
```

This opens the gui...

> To close the gui, use this

```js
gui.close();
```

...and this closes the gui.

These very simple methods open and close the gui, and neither take any arguments.

# Inventory

The Inventory object contains methods used for getting information about the user's inventory. It can be called
through `Player.getInventory()`. More about the `Player` class can be found [later](#player).

## Example

```js
function hasSponge() {
  const inventory = Player.getInventory();

  // The ID for sponge is 19.
  const spongeSlot = inventory.indexOf(19);

  if (spongeSlot !== -1) {
    ChatLib.chat("Sponge found in slot " + spongeSlot + "!");
  } else {
    ChatLib.chat("Sponge not found!");
  }
}
```

The example above lets us see if the inventory has a sponge item in it, and if so, say the slot it's in.
This works by first getting the inventory of the player, then it gets the slot ID of sponge, if there is any.
If any slot has a sponge, it will output the slot, otherwise it will return -1.

# KeyBinds

KeyBinds are used for detecting the state of a key.

<aside class="notice">These aren't meant to be used for Guis, it's preferable to register a keytyped method for detecting
if a player pressed a key inside your Gui</aside>

## Creation

> This is the preferred method to get a keybind

```js
const wKeyBind = Client.getKeyBindFromKey(Keyboard.KEY_W, "My W Key");
```

Let's break this down. We call the function `Client.getKeyBindFromKey` and save the result in a variable.
This will result in trying to find an existing Minecraft keybinding in order to try not to override it. If there
isn't one being used, it will create a new `KeyBind`, with the description and key we specified. We pass into this a keyCode from the [Keyboard](http://legacy.lwjgl.org/javadoc/org/lwjgl/input/Keyboard.html) class.

In our case, this will return the keybind already used by Minecraft for the run key (unless of course you changed yours
to a different key).

## Using the keybind

> To check if they key bind is being held, do this

```js
if (wKeyBind.isKeyDown()) {
  ChatLib.chat("Key is down!");
}
```

> To check if the key bind was pressed, use this

```js
if (wKeyBind.isPressed()) {
  ChatLib.chat("Key is pressed!");
}
```

This first example would spam your chat if it was in an Tick trigger or something of the like, as it will always be true
if you are holding down the key.

Now, in the second example, we would only get the chat message only once every time we press and hold the key. It only
returns true one time per key press. If you let go and press again, it will return true once more.

# Player

The `Player` object contains many methods used for retrieving information about the player.

## Location

> To get the direction the player is facing, use this

```js
const direction = Player.facing();
```

> To get the coordinates of the player, you can use this

```js
const playerX = Player.getX();
const playerY = Player.getY();
const playerZ = Player.getZ();
```

Using this in conjunction with displays, you can make a coordinates HUD.

```js
register("tick", locationTracker);

const display = new Display();
display.setRenderLoc(10, 10);

function locationTracker() {
  display.setLine(0, "X: " + Player.getX());
  display.setLine(1, "Y: " + Player.getY());
  display.setLine(2, "Z: " + Player.getZ());
}
```

In this case, we just made a display, and every tick it updates to show the player's location.

## LookingAt

> The `LookingAt` object was replaced with the `Player.lookingAt()` method.

This gets the current object that the player is looking at, whether that be a block or an entity. It returns
either the `Block`, `Sign`, `Entity` class, or an air `Block` when not looking at anything.

## Health and Various Stats

> You can not only get the player's health, hunger, active potion effects, but much more! Here's just a few
> examples of how to get each part.

```js
const health = Player.getHP();
const hunger = Player.getHunger();
const potions = Player.getActivePotionEffects();
const xpLevel = Player.getXPLevel();
```

## Armor

> To get the player's armor, you can use `Player.armor`

```js
const helmet = Player.armor.getHelmet();
```

This would return the item that is in the helmet slot of the player. This can be a pumpkin or any item
that is on the helmet slot.

## Getting Inventory information

> Here's an example showing how to find the durability and stack size of the item the player is holding.

```js
function displayHeldItemInfo() {
  const item = Player.getHeldItem();

  if (item.getName() !== "tile.air.name") {
    let durabilityPercentage = Math.ceil(
      (item.getDurability() / item.getMaxDamage()) * 100
    );

    // If NaN, that means it's a block
    if (isNaN(durabilityPercentage)) durabilityPercentage = "N/A (not a tool!)";

    ChatLib.chat("Item: " + item.getName());
    ChatLib.chat("Durability: " + durabilityPercentage);
    ChatLib.chat("Stack Size: " + item.getStackSize());
  } else {
    ChatLib.chat("&4You aren't holding anything!");
  }
}
```

In this case, we get the held item of the player. If the item isn't air, then it finds how damaged it is. If
the durability isn't a number, that means the item has to be a block. Then, if the player isn't holding anything, it
runs the last piece of the code, which is when the hand is empty.
If you want to get the player's inventory as a list of `Item`s, use `Player.getInventory()`.
