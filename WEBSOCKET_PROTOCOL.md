# WebSocket Protocol

This is documentation for the WebSocket Protocol implemented by BigRoom/vision and used by BigRoom/mystique to make real time communications possible.

**note: When first connecting to the WebSocket you need to authenticate yourself using the `access_token` parameter on the request. Eg: `var ws = WebSocket('http://beta.bigroom.co/api/ws?access_token=xxx.xxx.xxx')`**

## Channel Keys

A "Channel Key" is a way of describing a specific channel on a server. It is a combination of the host address and channel. Formed like the following:

`<ipv4_address>:6667/#<channel_name>`

### Examples

- `10.0.0.1:6667/#home`
- `102.0.10.1:6667/#roomtest`
- `10.0.0.100:6667/#p`

*TODO: The current parsing method relies on the connection being a) IPv4 and b) on port 6667. This should be made more flexible.*

## Incoming

The basic structure for an incoming websocket message is:

```json
{
    'name': string,
    'contents': object
}
```

- Name is the name of the event
- Object is the data which has been sent

The contents variable contains the data which is being processed.

### `CHANNELS`

Channels is used for retrieving a list of all of the channels which the current user is currently a part of.

```json
{
    'name': 'CHANNELS',
    'contents': [
        '#channel1',
        '#sekritchannel'
    ]
}
```

### `MESSAGE`

Message is used for receiving a message from the IRC server.

```json
{
    'name': 'MESSAGE',
    'contents': {
        'from': 'paked',
        'content': 'this is a message',
        'time': TIME,
        'channel': '#something'
        'host':'10.10.20.30'
    }
}
```

## Outgoing

The basic structure for an outgoing message is:

```json
{
    'name': string,
    'message': string,
    'channel': string
}
```

- The name is the event name
- Message is the message which is being sent (occasionally used for other data)
- Channel is the channel to be used. It is sent in Channel Key form (see above)

### `SET`

Set is how you join a channel over WebSockets

```json
{
    'name': 'SET',
    'message': 'CHANNEL KEY'
}
```

- Message is the channel key which you wish to join


### `SEND`

Send is how a message is sent from a WebSocket into the Zombies pool, into the actual IRC server

```json
{
    'name': 'SEND',
    'message': 'potato.',
    'channel': '10.10.10.10:6667/#roomtest'
}
```

- Message is the message that needs to be sent
- Channel is the Channel Key (see top) which the message should be sent to

### `CHANNELS`

Channels is the command which dispatches a command to get all of the channels a user is currently in.

```json
{
    'name': 'CHANNELS'
}
```
