# Slack Chat Bridge

Minimal bi-directional chat bridge between a Website Chat UI and a Slack Channel using FastAPI.

## Architecture

```
Website (WebSocket) ⇄ FastAPI ⇄ Slack Channel (Threads)
```

- **Website → Slack**: Messages from website users are posted to Slack threads
- **Slack → Website**: Replies in Slack threads are delivered to website users via WebSocket

## Features

- Real-time bi-directional messaging
- One Slack thread per website user
- In-memory storage (no database required)
- Event-driven and synchronous
- Comprehensive error logging

## Setup

1. **Install dependencies**:
   ```bash
   pip install -r requirements.txt
   ```

2. **Configure Slack**:
   
   See [SLACK_SETUP.md](SLACK_SETUP.md) for detailed step-by-step instructions.
   
   Quick summary:
   - Create a Slack App at https://api.slack.com/apps
   - Add Bot Token Scopes: `chat:write`, `channels:history`, `channels:read`
   - Enable Events API and subscribe to `message.channels` event
   - Install the app to your workspace
   - Add bot to your channel
   - Obtain: `SLACK_BOT_TOKEN`, `SLACK_SIGNING_SECRET`, `CHANNEL_ID`

3. **Set environment variables**:
   ```bash
   cp .env.example .env
   # Edit .env with your Slack credentials
   ```

4. **Run the server**:
   
   Option 1: Using the run script (recommended):
   ```bash
   python run.py
   ```
   
   Option 2: Using uvicorn directly:
   ```bash
   uvicorn app.main:app --reload --host 0.0.0.0 --port 8000
   ```

## Usage

### Website Integration

Connect to WebSocket endpoint:
```
ws://localhost:8000/ws/{user_id}
```

Send message:
```json
{
  "message": "Hello from website!"
}
```

Receive Slack replies:
```json
{
  "type": "slack_message",
  "message": "Reply from Slack",
  "thread_ts": "1234567890.123456"
}
```

### Slack Events API

The `/slack/events` endpoint handles Slack Events API callbacks. Configure this URL in your Slack App's Event Subscriptions settings.

## API Endpoints

- `GET /` - Health check
- `GET /health` - Health check with connection stats
- `POST /chat/send` - Send message to Slack thread
- `WS /ws/{user_id}` - WebSocket endpoint for website users
- `POST /slack/events` - Slack Events API endpoint
- `POST /push/{user_id}` - Server-side push to specific user
- `GET /static/chat.html` - Test client HTML page

## Storage

Uses in-memory storage mapping `website_user_id` → `slack_thread_ts`. Data is lost on server restart.

## Documentation

- [SLACK_SETUP.md](SLACK_SETUP.md) - Complete Slack App setup guide

