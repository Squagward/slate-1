# ChatLib

The ChatLib is a utility provided to imports for interacting with Minecraft chat. It has functionality to send, edit,
and delete messages in Minecraft's chat. You can create clickable, and hoverable, text in chat, run commands, and much
more.

## Sending messages

> This sends a client-side message in chat

```js
ChatLib.chat("Coming from the code!");
```

This first example shows how to display a basic chat message. This message differs from normal messages in that it does NOT trigger chat triggers. It is also not visible to other players.

## Message objects

> This example sends messages that have clickable and hoverable text

```js
const clickableMessage = new Message(
  "This is not clickable. ",
   new TextComponent("This is clickable").setClick("run_command", "/help"),
    "!"
);

const hoverableMessage = new TextComponent("This message does nothing when clicked.").setHoverValue("But it shows this text when hovered over!");

ChatLib.chat(clickableMessage);
ChatLib.chat(hoverableMessage);
```

Here we are creating new Message objects. These are required to send messages that have clickable or hoverable text.
The constructor of a Message can take as many `String`s or `TextComponent`s as you want, simply separate them with commas as shown in the first example. 

TextComponents are nice little wrappers that allow you to customize a small chunk of a message. You can change what happens when you click or hover on the message. You can also directly send a TextComponent as seen with the hoverable message. All of these can use formatting codes with the `&` symbol.

### Clickables

The first message we create is a message that has clickable, and non-clickable, text. The first part is regular text,
followed by a clickable part that runs the `/help` command when clicked, and shows the hoverText when the mouse is
hovering over that part of the message. Then, at the end, it has a non-clickable exclamation point.

### Hovers

The second message created and chatted is a message that only contains a hoverable message. Nothing will be activated
or ran when the message is clicked.

<aside class="notice">You can also use <code>Message#setRecursive(boolean)</code> to make Messages non-recursive.</aside>

## Message IDs

> This is how you send a chat message with an ID, and then delete it

```js
new Message("This will be deleted!").setChatLineId(5050).chat();

ChatLib.clearChat(5050);
```

Every message can have an ID specified, which can then be deleted from chat. This is best suited for auto-replacing menus,
chat messages you only want to display in chat for a certain amount of time, etc.

This example also showcases an alternative method of chatting a Message, as there is a simple helper method, `Message#chat()`

Only one chat message can have the same ID, as it will replace any messages with the same ID before sending.
The ID is specified in the message object, and you pass the same ID to `ChatLib.clearChat(id)` to delete it.

<aside class="notice">Doing <code>ChatLib.clearChat()</code> will delete all messages in chat, no matter the ID</aside>

## Editing chat

> This is how you edit a chat message after it has been sent to chat

```js
ChatLib.chat("Hey there! This will change...");

ChatLib.editChat("Hey there! This will change...", "And... changed!")
```

`ChatLib.editChat(message, replacer)` is a simple method that takes in an unformatted message and replaces all instances of it with the replacer. This is a _slightly_ laggy operation if done extremely rapidly (i.e. around 60 times per second). The `editChat` method can also take the Message ID as the first argument.

## Specially formatted messages

> This is how you center a chat message

```js
ChatLib.chat(ChatLib.getCenteredText("This is in the center of the chat!"));
```

> This is how you make a line break

```js
ChatLib.chat(ChatLib.getChatBreak("-"));
```

### Centered messages

To center a message in chat (padded by spaces), use the `ChatLib.getCenteredText(text)` method, and then chat what's
returned.

<aside class="warning">This is a <i>marginally</i> intensive operation, so if you can store this in a variable and re-use it, that's
preferable.</aside>

### Line breaks

To create a line break in chat that will be the exact length of the chat box no matter the user's width and size
settings, use the `ChatLib.getChatBreak(seperator)` method. This can take any seperator, like "-" as we used in the
example.

<aside class="success">Any length string can be used as the seperator, such as "seperate". However, because this is a long
string, there will be a gap at the end where another use of the string will not fit</aside>

## Chat message from event

> This is how you get the unformatted message from a chat event

```js
function onChatReceived(event) {
  const unformattedMessage = ChatLib.getChatMessage(event);
}
```

> This is how you get the formatted message from a chat event

```js
function onChatReceived(event) {
  const formattedMessage = ChatLib.getChatMessage(event, true);
}
```

### Unformatted chat

To get the unformatted chat from a chat event passed into a function by a Chat Trigger, pass it to the
`ChatLib.getChatMessage(event)` method, which returns an unformatted string.

### Formatted chat

However, if you want the formatted version of the chat message, append the `true` flag to the
`ChatLib.getChatMessage(event, formatted`) method.