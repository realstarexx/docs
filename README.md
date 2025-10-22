## Overview
### Features
| Feature | Description | Status |
|---------|-------------|--------|
| Command Handling | Process bot commands using decorators | Supported |
| Message Processing | Handle all text messages automatically | Supported |
| Media Files | Send images, documents, audio, and video files | Supported |
| Interactive Keyboards | Create both reply and inline keyboards | Supported |
| Message Management | Edit, delete, and pin messages | Supported |
| Polls and Games | Create polls and send dice games | Supported |

### Requirements
| Component | Version | Notes |
|-----------|---------|-------|
| Python | 3.7 or higher | Required |
| requests | 2.25.1 or higher | Required for HTTP requests |
| Storage | 50MB free space | For file operations |

## Getting Started
### Installation
Install the package using pip:
```bash
pip install sounava
```

### Setup
Create your first Telegram bot:
```python
from sounava import telegram

# Initialize bot with your token
bot = telegram("YOUR_BOT_TOKEN")

# Handle start command
@bot.command('/start')
def start_command(update, bot):
    bot.reply(update, "Bot started successfully!")

# Handle all text messages
@bot.message
def echo_message(update, bot):
    user_text = update['message']['text']
    bot.reply(update, f"You said: {user_text}")

# Start the bot
bot.start()
```
**The framework requires minimal configuration. The main requirement is a valid Telegram Bot Token obtained from @BotFather**

## Functions
### Bot Initialization
| Function   | Parameters       | Return Type | Description                                                |
|-------------|------------------|--------------|------------------------------------------------------------|
| `telegram()` | `token` (string) | Bot instance | Creates a new bot instance with the provided token.        |

**Examples**: (`Initialization`)

```python
from sounava import telegram

# Create bot instance
bot = telegram("123456:ABC-DEF1234ghIkl-zyx57W2v1u123ew11")

# Bot is now ready to handle commands and messages
```

### Message Handling
| Decorator         | Parameters           | Description                                               | Usage                              |
|--------------------|----------------------|-----------------------------------------------------------|------------------------------------|
| `@bot.command()`   | Command name (string) | Registers a function to handle specific bot commands       | Place above function definition    |
| `@bot.message`     | None                 | Registers a function to handle all text messages           | Place above function definition    |
| `@bot.callback`    | None                 | Registers a function to handle inline keyboard callbacks   | Place above function definition    |

**Examples**: (`Command Handler`)
```python
@bot.command('/start')
def start_handler(update, bot):
    user_id = update['message']['from']['id']
    bot.reply(update, f"Welcome! Your user ID is: {user_id}")

@bot.command('/help')
def help_handler(update, bot):
    help_text = """
Available Commands:
/start - Start the bot
/help - Show this help message
/info - Get bot information
"""
    bot.reply(update, help_text)
```

**Examples**: (`Message Handler`)
```python
@bot.message
def message_handler(update, bot):
    message_text = update['message']['text']
    
    # Simple response logic
    if message_text.lower() == 'hello':
        bot.reply(update, "Hi there!")
    elif message_text.lower() == 'bye':
        bot.reply(update, "Goodbye!")
    else:
        bot.reply(update, "I received your message")
```

**Examples**: (`Callback Handler`)
```python
@bot.callback
def callback_handler(update, bot):
    callback_data = update['callback_query']['data']
    user_id = update['callback_query']['from']['id']
    
    # Process callback data
    if callback_data == 'button_1':
        bot.answer_callback(update, "Button 1 clicked")
    elif callback_data == 'button_2':
        bot.answer_callback(update, "Button 2 clicked")
```

### Message Sending
| Function                              | Parameters                       | Description                                        | Return Type |
|---------------------------------------|-----------------------------------|----------------------------------------------------|--------------|
| `bot.reply(update, text)`              | `update`, `text` (string)         | Replies to the user's message                      | Dictionary  |
| `bot.send(chat_id, text)`              | `chat_id` (int), `text` (string)  | Sends a direct message to the specified chat        | Dictionary  |
| `bot.reply(update, text, reply_markup=keyboard)` | `update`, `text`, `reply_markup` | Replies with an interactive keyboard                | Dictionary  |

**Examples**: (`Basic Message Sending`)
```python
@bot.command('/send_demo')
def send_demo(update, bot):
    chat_id = update['message']['chat']['id']
    
    # Simple reply
    bot.reply(update, "This is a reply to your message")
    
    # Direct message
    bot.send(chat_id, "This is a direct message")
    
    # Message with formatting
    bot.reply(update, "*Bold text* and _italic text_", parse_mode="Markdown")
```

**Examples**: (`Message with Keyboard`)
```python
@bot.command('/menu')
def show_menu(update, bot):
    # Create a simple keyboard
    keyboard = [
        ["Option 1", "Option 2"],
        ["Option 3", "Option 4"]
    ]
    
    bot.reply(update, "Please choose an option:", reply_markup=keyboard)
```

## Core Features
### Media Support
| Function                         | Parameters              | Description                     | File Types     |
|----------------------------------|--------------------------|----------------------------------|----------------|
| `send_photo(chat_id, photo_path)` | `chat_id`, `photo_path`  | Sends an image file              | JPG, PNG, GIF  |
| `reply_photo(update, photo_path)` | `update`, `photo_path`   | Replies with an image file       | JPG, PNG, GIF  |
| `send_document(chat_id, document_path)` | `chat_id`, `document_path` | Sends a document file            | PDF, TXT, ZIP  |
| `reply_document(update, document_path)` | `update`, `document_path`  | Replies with a document file     | PDF, TXT, ZIP  |
| `send_audio(chat_id, audio_path)` | `chat_id`, `audio_path`  | Sends an audio file              | MP3, OGG, M4A  |
| `reply_audio(update, audio_path)` | `update`, `audio_path`   | Replies with an audio file       | MP3, OGG, M4A  |
| `send_video(chat_id, video_path)` | `chat_id`, `video_path`  | Sends a video file               | MP4, AVI, MOV  |
| `reply_video(update, video_path)` | `update`, `video_path`   | Replies with a video file        | MP4, AVI, MOV  |

**Examples**: (`Sending Media Files`)
```python
@bot.command('/send_media')
def send_media(update, bot):
    chat_id = update['message']['chat']['id']
    
    # Send different types of media
    bot.send_photo(chat_id, "welcome.jpg")
    bot.send_document(chat_id, "information.pdf")
    bot.send_audio(chat_id, "audio_message.mp3")
    bot.send_video(chat_id, "demo_video.mp4")

@bot.command('/reply_media')
def reply_media(update, bot):
    # Reply with media files
    bot.reply_photo(update, "response_image.jpg")
    bot.reply_document(update, "data.txt")
```

### Keyboards
Sounava supports two types of keyboards:

1. **Reply Keyboards**:

· Appear at the bottom of the chat
· Simple text buttons
· Automatically hide after selection

2. **Inline Keyboards**:

· Appear below the message
· Can have callback data
· Support URLs and other actions

**Keyboard Types Comparison**:

Feature Reply Keyboard Inline Keyboard
Position Bottom of chat Below message
Persistence Can be persistent Message-specific
Button Types Text only Text, URL, Callback
Use Case Menu navigation Interactive actions

**Examples**: (`Reply Keyboard`)
```python
@bot.command('/show_keyboard')
def show_reply_keyboard(update, bot):
    keyboard = [
        ["Button 1", "Button 2", "Button 3"],
        ["Button 4", "Button 5"],
        ["Cancel"]
    ]
    
    bot.reply(update, "Choose an option from the keyboard:", reply_markup=keyboard)
```

**Examples**: (`Inline Keyboard`)
```python
@bot.command('/show_inline')
def show_inline_keyboard(update, bot):
    inline_keyboard = [
        [
            {"text": "Option 1", "callback_data": "opt1"},
            {"text": "Option 2", "callback_data": "opt2"}
        ],
        [
            {"text": "Google", "url": "https://google.com"},
            {"text": "Cancel", "callback_data": "cancel"}
        ]
    ]
    
    bot.reply(update, "Select an option:", reply_markup=inline_keyboard)
```

### Message Operations
| Function                                 | Parameters                     | Description               | Use Case               |
|------------------------------------------|---------------------------------|---------------------------|-------------------------|
| `edit_message(chat_id, message_id, new_text)` | `chat_id`, `message_id`, `new_text` | Edits an existing message | Update information      |
| `delete_message(chat_id, message_id)`     | `chat_id`, `message_id`         | Deletes a message         | Remove content          |
| `pin_message(chat_id, message_id)`        | `chat_id`, `message_id`         | Pins a message in chat    | Important notices       |
| `unpin_message(chat_id, message_id)`      | `chat_id`, `message_id`         | Unpins a message          | Clear pinned messages   |

**Examples**: (`Message Management`)
```python
@bot.command('/edit_demo')
def edit_demo(update, bot):
    chat_id = update['message']['chat']['id']
    
    # Send initial message
    response = bot.reply(update, "This message will be edited...")
    message_id = response['result']['message_id']
    
    # Edit after 2 seconds
    import time
    time.sleep(2)
    bot.edit_message(chat_id, message_id, "Message has been updated!")

@bot.command('/cleanup')
def cleanup(update, bot):
    chat_id = update['message']['chat']['id']
    message_id = update['message']['message_id']
    
    # Delete the command message
    bot.delete_message(chat_id, message_id)
    
    # Send and pin a new message
    new_msg = bot.send(chat_id, "This is an important message")
    bot.pin_message(chat_id, new_msg['result']['message_id'])
```

### API Reference
| Category              | Functions                                                                                                                | Description            |
|------------------------|--------------------------------------------------------------------------------------------------------------------------|------------------------|
| Initialization         | `telegram()`                                                                                                            | Create bot instance    |
| Decorators             | `@bot.command()`, `@bot.message`, `@bot.callback`                                                                       | Register handlers      |
| Message Sending        | `reply()`, `send()`                                                                                                     | Send text messages     |
| Media Sending          | `send_photo()`, `reply_photo()`, `send_document()`, `reply_document()`, `send_audio()`, `reply_audio()`, `send_video()`, `reply_video()` | Send media files       |
| Interactive Features   | `send_poll()`, `reply_poll()`, `send_dice()`, `reply_dice()`, `send_location()`, `reply_location()`, `send_contact()`, `reply_contact()` | Interactive elements   |
| Message Operations     | `edit_message()`, `delete_message()`, `pin_message()`, `unpin_message()`                                                | Manage messages        |
| Callback Handling      | `answer_callback()`                                                                                                     | Respond to callbacks   |
| Bot Control            | `start()`, `stop()`, `get_me()`      | Bot management         |

| Parameter       | Type       | Required | Description              | Example               |
|-----------------|-------------|-----------|--------------------------|------------------------|
| `token`         | string      | Yes       | Telegram Bot Token       | `"123456:ABC-DEF..."` |
| `update`        | dictionary  | Yes       | Telegram update object   | Received from webhook  |
| `chat_id`       | integer     | Yes       | Target chat ID           | `123456789`           |
| `message_id`    | integer     | Yes       | Target message ID        | `123`                 |
| `text`          | string      | Yes       | Message text content     | `"Hello world"`       |
| `photo_path`    | string      | Yes       | Path to image file       | `"image.jpg"`         |
| `document_path` | string      | Yes       | Path to document file    | `"file.pdf"`          |
| `audio_path`    | string      | Yes       | Path to audio file       | `"audio.mp3"`         |
| `video_path`    | string      | Yes       | Path to video file       | `"video.mp4"`         |
| `reply_markup`  | list        | No        | Keyboard layout          | `[["Button"]]`        |
| `parse_mode`    | string      | No        | Text formatting          | `"Markdown"`          |


**Example**: (`Sample Bot Script`)
```python
from sounava import telegram

# Initialize bot
bot = telegram("YOUR_BOT_TOKEN")

# Command handlers
@bot.command('/start')
def start_handler(update, bot):
    keyboard = [
        [{"text": "Help", "callback_data": "help"}],
        [{"text": "About", "callback_data": "about"}]
    ]
    bot.reply(update, "Welcome to the bot!", reply_markup=keyboard)

@bot.command('/help')
def help_handler(update, bot):
    help_text = """
Available Commands:
/start - Start the bot
/help - Show help information
/info - Bot information
"""
    bot.reply(update, help_text)

# Message handler
@bot.message
def message_handler(update, bot):
    user_text = update['message']['text']
    if user_text:
        bot.reply(update, f"Received: {user_text}")

# Callback handler
@bot.callback
def callback_handler(update, bot):
    data = update['callback_query']['data']
    if data == "help":
        bot.answer_callback(update, "Help information would appear here")
    elif data == "about":
        bot.answer_callback(update, "This is Sounava Bot Framework")

# Start the bot
print("Bot is starting...")
bot.start()
```
