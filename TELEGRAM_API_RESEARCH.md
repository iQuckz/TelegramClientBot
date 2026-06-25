# Telegram Bot API Research & Implementation Guide

## API Overview

The Telegram Bot API is an HTTP-based interface for building bots. All requests must be made over HTTPS to `https://api.telegram.org/bot<token>/METHOD_NAME`.

### Authentication
Each bot receives a unique token when created (format: `123456:ABC-DEF1234ghIkl-zyx57W2v1u123ew11`). This token is used in all API requests.

### Request Methods
The API supports both GET and POST HTTP methods with four ways to pass parameters:
- URL query string
- application/x-www-form-urlencoded
- application/json (except for file uploads)
- multipart/form-data (for file uploads)

### Response Format
All responses contain a JSON object with:
- `ok` (Boolean): Indicates if the request was successful
- `result` (Object): Contains the result if `ok` is true
- `description` (String): Human-readable error message if `ok` is false
- `error_code` (Integer): Error code for handling

## Update Delivery Methods

### 1. Long Polling (getUpdates)
The bot periodically calls the `getUpdates` method to fetch pending updates. This is the simpler approach but less efficient as it requires constant polling.

**Pros:**
- Simple to implement
- Works behind firewalls and NAT
- No need for public server

**Cons:**
- Higher bandwidth usage
- More API calls
- Higher latency

### 2. Webhooks
The bot provides a webhook URL where Telegram sends updates via HTTP POST requests. This is more efficient for production.

**Pros:**
- Lower bandwidth and API calls
- Instant update delivery
- More efficient for production

**Cons:**
- Requires public HTTPS server
- More complex setup
- Firewall/NAT considerations

## Core API Methods for Bot Client

### Getting Bot Information
- **getMe**: Returns basic information about the bot (name, username, ID)

### Receiving Updates
- **getUpdates**: Fetches pending updates (messages, callbacks, etc.)
- **setWebhook**: Configures webhook URL for receiving updates
- **deleteWebhook**: Removes webhook configuration

### Sending Messages
- **sendMessage**: Send text messages
- **sendPhoto**: Send photos
- **sendDocument**: Send documents/files
- **sendAudio**: Send audio files
- **sendVideo**: Send video files
- **sendVoice**: Send voice messages
- **sendAnimation**: Send animated GIFs
- **sendMediaGroup**: Send multiple media items in one message

### Message Management
- **editMessageText**: Edit text of sent messages
- **editMessageMedia**: Edit media in sent messages
- **deleteMessage**: Delete messages

### Chat Management
- **getChat**: Get chat information
- **getChatMember**: Get information about a specific chat member
- **getChatAdministrators**: Get list of administrators in a chat
- **sendChatAction**: Show typing/upload indicator (typing, upload_photo, upload_document, etc.)

### Commands
- **setMyCommands**: Set list of commands for the bot
- **getMyCommands**: Get list of commands
- **deleteMyCommands**: Delete commands

### Inline Keyboards & Buttons
- **sendMessage** with `reply_markup`: Send messages with inline or reply keyboards
- **editMessageReplyMarkup**: Edit keyboard of existing message
- **answerCallbackQuery**: Respond to button clicks

### Inline Queries
- **answerInlineQuery**: Respond to inline queries from users

## Update Types

The `getUpdates` method returns updates with various types:

| Update Type | Description |
|------------|-------------|
| `message` | New incoming message |
| `edited_message` | Message was edited |
| `channel_post` | New post in a channel |
| `edited_channel_post` | Channel post was edited |
| `inline_query` | Inline query from user |
| `chosen_inline_result` | User selected an inline result |
| `callback_query` | User clicked an inline button |
| `shipping_query` | User initiated checkout |
| `pre_checkout_query` | User confirmed payment |
| `poll` | Poll state changed |
| `poll_answer` | User answered a poll |
| `my_chat_member` | Bot was added/removed from chat |
| `chat_member` | Member joined/left chat |
| `chat_join_request` | User requested to join chat |

## Message Types

Users can send various types of messages to bots:
- Text messages
- Photos
- Documents
- Audio files
- Video files
- Voice messages
- Animations (GIFs)
- Stickers
- Locations
- Contacts
- Polls
- Dice

## Inline Keyboards

Inline keyboards display buttons directly below messages. Button types include:
- **Callback buttons**: Send data back to bot when clicked
- **URL buttons**: Open URLs
- **Switch inline buttons**: Trigger inline mode
- **Pay buttons**: For payment integration
- **Web App buttons**: Open Web Apps

## Message Formatting

Bots can format messages using:
- **Markdown**: `*bold* _italic_ [link](url)`
- **HTML**: `<b>bold</b> <i>italic</i> <a href="url">link</a>`
- **Rich Text**: Advanced formatting with multiple styles (Bot API 10.1+)

## File Handling

### Uploading Files
- Use multipart/form-data to upload files
- Maximum file size: 50 MB for most file types
- Local Bot API Server: Up to 2000 MB

### Downloading Files
- Get file info using `getFile` method
- Download from `https://api.telegram.org/file/bot<token>/<file_path>`

## Bot Features

### Commands
Commands start with `/` and can be up to 32 characters. Examples: `/start`, `/help`, `/settings`.

### Command Scopes
Different commands can be shown to different users/groups:
- Default scope
- Private chats
- Group chats
- Group administrators
- Channel administrators

### Menu Button
A menu button appears near the message field that can:
- Show a list of commands
- Launch a Web App

### Global Commands
All bots should support:
- `/start`: Begin interaction
- `/help`: Show help message
- `/settings`: Show settings (if applicable)

## Recent API Updates (Bot API 10.1 - June 2026)

### Rich Messages
- Support for highly structured text formatting
- Rich text blocks (paragraphs, tables, code, etc.)
- Support for streaming AI-generated replies

### Join Request Queries
- Handle chat join requests
- Support for join request Web Apps

### Enhanced Polls
- Multiple correct answers in quizzes
- Poll media support
- Persistent poll option IDs
- Poll descriptions

### Live Photos
- Support for photos with short videos
- Live photo in media groups

## Implementation Strategy for Mobile App

1. **Authentication Phase**
   - Store bot token securely using Expo SecureStore
   - Validate token via `getMe` API call
   - Display bot info on dashboard

2. **Update Handling Phase**
   - Implement polling via `getUpdates` with configurable interval
   - Parse different update types (messages, callbacks, etc.)
   - Store updates locally for offline access

3. **Message Sending Phase**
   - Build UI for text input and formatting
   - Implement `sendMessage` for text
   - Add support for media sending (`sendPhoto`, `sendDocument`, etc.)

4. **Advanced Features Phase**
   - Implement inline keyboard builder
   - Add callback query handling
   - Support message editing and deletion
   - Implement chat management features

5. **Webhook Configuration Phase**
   - Allow users to configure webhook URL
   - Implement `setWebhook` API call
   - Display webhook status and test functionality

## API Rate Limits

Telegram Bot API has rate limits to prevent abuse:
- Approximately 30 messages per second per chat
- Approximately 300 messages per minute per chat
- Approximately 20 messages per minute for broadcast messages

## Error Handling

Common error codes:
- `400`: Bad Request (invalid parameters)
- `401`: Unauthorized (invalid token)
- `403`: Forbidden (bot not in chat)
- `404`: Not Found (chat/message not found)
- `429`: Too Many Requests (rate limited)

Implement retry logic with exponential backoff for `429` errors.
