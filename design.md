# Telegram Bot Client - Design Plan

## Overview
A mobile app that allows Telegram bots to log in using their API tokens and perform all standard bot operations through an intuitive mobile interface.

## Screen List

### 1. **Login Screen**
- Bot token input field (password-protected)
- "Connect Bot" button
- Loading state during token validation
- Error display for invalid tokens
- Info text explaining how to get a bot token

### 2. **Dashboard / Home Screen**
- Bot info card (name, username, avatar)
- Quick stats (messages sent, chats managed, pending updates)
- Navigation tabs for main features
- Logout button

### 3. **Chats Screen**
- List of active chats with the bot
- Chat preview (last message, timestamp, unread count)
- Search/filter functionality
- Tap to open chat detail

### 4. **Chat Detail Screen**
- Message history (scrollable)
- Message input field with send button
- Inline keyboard buttons (if bot sends them)
- User info display
- Options menu (clear chat, block user, etc.)

### 5. **Send Message Screen**
- Target selection (user ID, chat ID, or channel)
- Message text input
- Media attachment options (photo, document, audio)
- Formatting options (bold, italic, code)
- Send button with confirmation

### 6. **Updates / Webhook Screen**
- Current polling/webhook status
- Webhook URL display (if configured)
- Toggle polling on/off
- Set webhook URL
- View recent updates log
- Test webhook button

### 7. **Bot Commands Screen**
- List of registered commands
- Add/edit/delete commands
- Command description and scope
- Test command button

### 8. **Settings Screen**
- Bot token management (change/reset)
- Notification preferences
- Dark/light theme toggle
- Language selection
- About section

## Primary Content and Functionality

### Dashboard
- Displays bot profile (name, username, profile picture)
- Shows real-time stats: total chats, pending messages, last update time
- Quick action buttons: View Chats, Send Message, Configure Webhook

### Chats Management
- Fetches active chats using `getUpdates` API
- Displays chat list with preview of last message
- Shows unread count and timestamp
- Tap to view full chat history and send messages

### Message Sending
- Input field for message text
- Support for Markdown and HTML formatting
- Inline keyboard builder (for interactive buttons)
- File upload (photos, documents, audio, video)
- Send to specific user, group, or channel

### Updates Handling
- **Polling Mode**: Periodically calls `getUpdates` to fetch new messages
- **Webhook Mode**: Receives updates via webhook (requires backend)
- Display update log with timestamps and content
- Manual update fetch button

### Bot Commands
- View all registered commands
- Add new commands with descriptions
- Set command scope (default, private chats, group chats)
- Delete commands

## Key User Flows

### Flow 1: Bot Login
1. User opens app → Login screen
2. User enters bot token
3. App validates token via `getMe` API call
4. On success: Navigate to Dashboard
5. On failure: Show error message, allow retry

### Flow 2: View Chats and Send Message
1. User taps "Chats" tab → Chats screen
2. App fetches chats from `getUpdates`
3. User taps a chat → Chat Detail screen
4. User types message in input field
5. User taps "Send" → Message sent via `sendMessage` API
6. Message appears in chat history

### Flow 3: Configure Webhook
1. User navigates to Updates/Webhook screen
2. User toggles between Polling and Webhook mode
3. If Webhook: User enters webhook URL and secret
4. App calls `setWebhook` API
5. Display confirmation and webhook status

### Flow 4: Send Media
1. User on Send Message screen
2. User taps "Attach" button
3. System file picker opens
4. User selects photo/document/audio
5. Preview displayed
6. User taps "Send" → File uploaded and sent via `sendPhoto`, `sendDocument`, etc.

## Color Choices

| Element | Color | Usage |
|---------|-------|-------|
| Primary | `#0a7ea4` | Buttons, active states, highlights |
| Background | `#ffffff` (light) / `#151718` (dark) | Screen background |
| Surface | `#f5f5f5` (light) / `#1e2022` (dark) | Cards, input fields |
| Text Primary | `#11181C` (light) / `#ECEDEE` (dark) | Main text |
| Text Secondary | `#687076` (light) / `#9BA1A6` (dark) | Hints, labels |
| Success | `#22C55E` | Message sent, operation success |
| Error | `#EF4444` | Errors, invalid states |
| Border | `#E5E7EB` (light) / `#334155` (dark) | Dividers, borders |

## Telegram Bot API Integration Points

### Core APIs Used
- `getMe` - Get bot info
- `getUpdates` - Fetch pending updates (polling)
- `setWebhook` - Configure webhook
- `sendMessage` - Send text messages
- `sendPhoto`, `sendDocument`, `sendAudio`, `sendVideo` - Send media
- `editMessageText` - Edit sent messages
- `deleteMessage` - Delete messages
- `getChat` - Get chat info
- `getChatMember` - Get member info
- `sendChatAction` - Show typing/upload indicator
- `answerInlineQuery` - Handle inline queries
- `answerCallbackQuery` - Handle button clicks

### Update Handling
- Message updates
- Callback query updates (button clicks)
- Inline query updates
- Chosen inline result updates
- Chat member updates

## Technical Considerations

### State Management
- Use React Context for bot session state
- Store bot token securely in Expo SecureStore
- Cache chat list and message history locally
- Sync with API on app focus

### Performance
- Implement pagination for chat list and message history
- Debounce search/filter operations
- Use FlatList for efficient list rendering
- Background polling with configurable intervals

### Error Handling
- Display clear error messages for API failures
- Implement retry logic with exponential backoff
- Show connection status indicator
- Handle token expiration gracefully

### Security
- Store bot token in secure storage (not AsyncStorage)
- Validate all API responses
- Sanitize user input before sending to API
- Use HTTPS for all API calls
