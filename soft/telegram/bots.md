## Telegram Bots

> References:
> https://core.telegram.org/bots
> https://core.telegram.org/bots/api
> https://core.telegram.org/bots/faq
> https://github.com/python-telegram-bot/python-telegram-bot/wiki
> https://habr.com/ru/post/543676/

### Description
Telegram Bots are special accounts that do not require an additional phone number to set up. Users can interact with bots in two ways:
- Send messages and commands to bots by opening a chat with them or by adding them to groups.
- Send requests directly from the input field by typing the bot's @username and a query. This allows sending content from inline bots directly into any chat, group or channel.

Messages, commands and requests sent by users are passed to the software running on your servers. Our intermediary server handles all encryption and communication with the Telegram API for you. 

### How do I create a bot?
https://t.me/botfather
The **name** of your bot is displayed in contact details and elsewhere.
The **Username** is a short name, to be used in mentions and t.me links.
The **token** is required to authorize the bot and send requests to the [Bot API](https://core.telegram.org/bots/api).
Botfather will send status alerts  if it sees something is wrong (abnormally low readings).

### Inline mode
Users can interact with your bot via [**inline queries**](https://core.telegram.org/bots/api#inline-mode) straight from the **text input field** in **any** chat. All they need to do is start a message with your bot's username and then type a query. To enable this option, send the `/setinline` command to [@BotFather](https://telegram.me/botfather) and provide the placeholder text that the user will see in the input field after typing your bot’s name.

### Keyboards
Whenever your bot sends a message, it can pass along a special keyboard with predefined reply options (see [ReplyKeyboardMarkup](https://core.telegram.org/bots/api/#replykeyboardmarkup)). 

### Commands
A command must always start with the '/' symbol and may not be longer than 32 characters. Commands can use latin letters, numbers and  underscores. Here are a few examples:
```
/command

/start@TriviaBot
/start@ApocalypseBot
```

### Formatting
You can use bold, italic or fixed-width text, as well as inline links in your bots' messages. 

### Privacy mode
A bot running in privacy mode will not receive all messages that people send to the group. Instead, it will only receive:
- Messages that start with a slash '/'
- Replies to the bot's own messages
- Service messages (people added or removed from the group, etc.)
- Messages from channels where it's a member

Privacy mode is enabled by default for all bots, except bots that were added to the group as **admins**. It can be disabled, so that the bot receives all messages like an ordinary user (the bot will need to be **re-added** to the group).

#### What messages will my bot get?

1. **All bots**, regardless of settings, will receive:

- All service messages.
- All messages from private chats with users.
- All messages from channels where they are a member.

2. **Bot admins** and bots with [privacy mode](https://core.telegram.org/bots#privacy-mode) **disabled** will receive all messages except messages sent by other bots.

3. Bots with [privacy mode](https://core.telegram.org/bots#privacy-mode) **enabled** will receive:

- Commands explicitly meant for them (e.g., /command@this_bot).
- General commands from users (e.g. /start) **if** the bot was the last bot to send a message to the group.
- Messages sent [via](https://core.telegram.org/bots/api#inline-mode) this bot.
- Replies to any messages implicitly or explicitly meant for this bot.

### Deep linking
Telegram bots have a [deep linking](https://en.wikipedia.org/wiki/Deep_linking) mechanism, that allows for passing additional parameters to the bot on startup. Each bot has a link that opens a conversation with it in Telegram — `https://t.me/<bot username>`. You can add the parameters **start** or **startgroup** to this link, with values up to 64 characters long. For example:
```
https://t.me/triviabot?startgroup=test
A-Z, a-z, 0-9, _ and - are allowed.
```

### Location and Number
Bots can ask a user for their **location** and **phone number** using special buttons. Note that both phone number and location request buttons will only work in private chats. Use the `/setinlinegeo` command with [@BotFather](https://telegram.me/botfather) to enable this.

### Getting Updates
You can either use [long polling](https://core.telegram.org/bots/api#getupdates) or [Webhooks](https://core.telegram.org/bots/api#setwebhook). Please note that it's **not** possible to get updates via long polling while an outgoing Webhook is set.

### Making requests
All queries to the Telegram Bot API must be served over HTTPS and need to be presented in this form: `https://api.telegram.org/bot<token>/METHOD_NAME`. Like this for example:
```
https://api.telegram.org/bot123456:ABC-DEF1234ghIkl-zyx57W2v1u123ew11/getMe
```

We support **GET** and **POST** HTTP methods. 

We support four ways of passing parameters in Bot API requests:

- [URL query string](https://en.wikipedia.org/wiki/Query_string)
- application/x-www-form-urlencoded
- application/json (except for uploading files)
- multipart/form-data (use to upload files)