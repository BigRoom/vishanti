# HTTP Protocol

The HTTP Protocol is the API which is used for performing non realtime actions like managing users, getting servers and authentication.

The API attempts to follow RESTful practices.

Responses are made using JSON and follow the following template:

```js
{
    message: string,
    error: boolean,
    code: int,
    data: object
}
```

- Message is something the server has to say about a certain event. Mostly used with errors
- Error `true` when an error occurred on the server, default behaviour should be to then display the `message` value to the user
- Code the HTTP status code
- Data is the payload of the response. It can contain anything.

## Users

### Login - GET /api/users?username=XXX&password=YYY

The login route is used to retrieve a token which can be used to access private routes, connect to the WebSocket API and get chat history.

#### Response

```js
{
    message: '...something...',
    error: false,
    code: 200,
    data: 'XXX.XXX.XXX'
}
```

- The data is a JSON Web Token. It is used to make authenticated requests to the HTTP server

### Register - POST /api/users?username=XXX&password=YYY&email=ZZZ

The register route is used to create a new user and receive information about it.

*note: A subsequent request to the Login route should be made to receieve a token*

#### Response

```js
{
    message: '...',
    error: false,
    code: 200,
    data: {
        id: int,
        username: string,
        email: string
    }
}
```

- Data contains the user object which has been created

### Current User - POST| GET /api/users/me?access_token=XXX.XXX.XXX

The current user route gets the user that belongs to the access token

#### Response

```js
{
    message: '...',
    error: false,
    code: 200,
    data: {
        id: int,
        username: string,
        email: string
    }
}
```

- Data contains the user which was found with that JWT

## WebSockets

### Connect to WebSocket - GET/POST/ETC. /api/ws?access_token=XXX.XXX.XXX

The WebSocket route connects an authenticated user to their IRC bot. The WebSocket protocol can be found in [WEBSOCKET_PROTOCOL.md](WEBSOCKET_PROTOCOL.md).

## Servers

### Get Default Server - GET /api/servers/default

The get default server provides the web client the IP of the IRC Host they should connect to by default.

*note: Currently it is assumed that the port is on `6667`*

#### Response

```js
{
    message: '...',
    error: false,
    code: int,
    data: {
        server: string
    }
}
```

- Data provides an IP address which the API can be used to connect to the WebSocket with.

### Scrollback - GET /api/servers/{host}/{channel_name}/scrollback?access_token=XXX.XXX.XXX&channel_key=YYY:6667/#CHANNEL&page=N

This route gets the scrollback from a certain channel.

*note: the channel_name URL variable should **not** include the `#` symbol*

#### Response

```js
{
    message: '...',
    error: false,
    code: int,
    data: [
        {
            id: int,
            message: string,
            username: string,
            channel_key: string,
            time: int
        }
    ]
}
```

- Data contains an array of messages
- Time is the time the message was sent in Unix Nanoseconds
- Channel_key is the channel_key which the message was dispatched to (See [WEBSOCKETS_PROTOCOL.md](WEBSOCKETS_PROTOCOL.md))
