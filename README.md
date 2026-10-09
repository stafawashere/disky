# Disky

A Discord client library I wrote in Python without using discord.py or any other wrapper. It talks to Discord's Gateway over a raw WebSocket and to the REST API with plain HTTP requests. I built it because I wanted to see exactly what goes over the wire when the official client connects, instead of trusting a library to hide it.

## What it handles

- Opening the Gateway connection, sending IDENTIFY, and running the heartbeat loop on the interval Discord sends back
- Reconnecting when Discord asks for it or the session goes invalid (it starts a fresh session rather than resuming)
- Parsing 57 Gateway event types into an `Event` object you can check with properties like `event.READY` or `event.MESSAGE_CREATE`
- Sending, editing, deleting, pinning and reacting to messages over REST
- Optionally dumping every Gateway payload to `./log` as JSON, which is how I learned most of the protocol

## Using it

The code imports itself as `diskyv2`, so clone it into a folder with that name.

```bash
git clone https://github.com/stafawashere/disky.git diskyv2
pip install requests websockets beautifulsoup4 colorama emoji
```

```python
from diskyv2 import Client

client = Client(token="YOUR_TOKEN", log_to_console=True)

async def on_event(event):
   if event.MESSAGE_CREATE:
      print(event.data["content"])

client.event_listener.set_callback(on_event)
client.start()
```

## A note on Discord's terms

Disky logs in as a user account, not a bot account. Automating a user account is against Discord's terms of service and can get the account banned. I wrote it to learn how the protocol works, not to run it on a real account.

## Files

- `client.py` the WebSocket session, heartbeat, reconnects and REST calls
- `objects.py` the event parser, the listener and the user object
- `utility.py` opcodes, client properties, build number and nonce helpers
- `logger.py` colored console output
- `errors.py` the exceptions raised when login fails
