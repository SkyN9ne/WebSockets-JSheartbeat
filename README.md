<a href="https://www.npmjs.com/package/websocket-heartbeat-js" alt="NPM latest version"><img src="https://img.shields.io/npm/v/websocket-heartbeat-js.svg"></a>
<a href="https://npms.io/search?q=websocket-heartbeat-js" alt="NPM latest version"><img src="https://badges.npms.io/websocket-heartbeat-js.svg"></a>
<a href="https://deepscan.io/dashboard/#view=project&pid=3358&bid=29734"><img src="https://deepscan.io/api/projects/3358/branches/29734/badge/grade.svg" alt="DeepScan Grade"></a>
<a href="https://www.npmjs.com/package/websocket-heartbeat-js" alt="NPM total downloads"><img src="https://img.shields.io/npm/dt/websocket-heartbeat-js.svg"></a>
<a href="https://github.com/zimv/websocket-heartbeat-js" alt="Github stars"><img src="https://img.shields.io/github/stars/zimv/websocket-heartbeat-js.svg?style=social&label=Star"></a>
<a href="https://github.com/zimv/websocket-heartbeat-js" alt="Github forks"><img src="https://img.shields.io/github/forks/zimv/websocket-heartbeat-js.svg?style=social&label=Fork"></a>
<a href="https://github.com/zimv/websocket-heartbeat-js" alt="Github contributors"><img src="https://img.shields.io/github/contributors/zimv/websocket-heartbeat-js.svg"></a>
# A JavaScript browser WebSockets Heartbeater
----------------------

## Introduction
The `websocket-heartbeat-js` is based on **`WebSockets`** technology on web browsers using JavaScript.
The main purpose is to ensure a web client and server connection, and it has a mechanism of heartbeat detection and automatic reconnection. 
When the client device has a network outage or server error which causes **`WebSockets`** to disconnect, the program will automatically reconnect until reconnection is successful again.

## Why
When we use the native **`WebSockets`**, if the network disconnects, any `event` function not be executed. So in that case the front-end program won't know that the **`WebSocket`** was disconnected. 
But if the program is now executing ***`WebSocket.send()`***, the browser should discover that the message signal failed, so then the `onclose` function will execute.


The Back-end **`WebSocket`** service is likely to fail or error out, when the **`WebSocket`** disconnected in a way that the front-end won't notice any messages received. 
So we need to send `ping` requests / messages by `timeout`. The Server returns a `pong` message to the client when the server received a `ping` message. Because it received the `pong` message, the client knows the connection is normal and fine. 
If the client didn't receive the `pong` message, there's likely something wrong with the connection and it will attempt to reconnect

In summary, for solving the above 2 problems the client should initiate and send a `ping` message for checking the connection status.

## How

***1.Close `WebSockets` connection***

If a **`WebSocket`** needs to disconnect, client must execute `WebsocketHeartbeatJs.close()`. If the server wants to disconnect, it should send a `close` message to the client. 
When the client receives a `close` message that it's to execute
`WebsocketHeartbeatJs.close()`. 

## **Example:**

```javascript
websocketHeartbeatJs.onmessage = (e) => {
if(e.data == 'close')
websocketHeartbeatJs.close();
}
```
 
***2. `Ping` & `Pong`***

The server should `return` a `pong` message when the client sends a `ping` message. The `pong` message can be of any value. 
`websocket-heartbeat-js` will not handle `pong` messages, instead it will only reset the heartbeat after receiving any message, as receiving any message means that the connection is normal and working as intended.

## **Installation:**
```console
npm install websocket-heartbeat-js
```

## **Importing**:
```javascript

import WebsocketHeartbeatJs from 'websocket-heartbeat-js';

let websocketHeartbeatJs = new WebsocketHeartbeatJs({
    url: 'ws://xxxxxxx'
});

websocketHeartbeatJs.onopen = function () {
    console.log('STATUS: Successfully connected');

websocketHeartbeatJs.send('hello server');
}

websocketHeartbeatJs.onmessage = function (e) {
    console.log(`onmessage: ${e.data}`);
}

websocketHeartbeatJs.onreconnect = function () {
    console.log('STATUS: Reconnecting...');
}

```

### **Using the script**:
```javascript

<script src="./node_modules/websocket-heartbeat-js/dist/index.js">
</script>

let websocketHeartbeatJs = new window.WebsocketHeartbeatJs({
    url: 'ws://xxxxxxx'
});

```

## **API**:

#### **`websocketHeartbeatJs.ws`**

This `websocketHeartbeatJs.ws` is a native **`WebSocket`** instance. If you need more native **`WebSocket`** features, use the `websocketHeartbeatJs.ws`

```javascript
websocketHeartbeatJs.ws == WebSocket(websocketHeartbeatJs.opts.url);
```

####  **`websocketHeartbeatJs.opts`**
(Object)
    
| Attribute | required | type | default | description |
| ------ | ------ | ------ | ------ | ------ |
| `url` | `true` | `string` | `none` | `WebSocket` service address |
| `protocols` | `false` | `string` or `string[]` | `none` | `new WebSocket`(protocols)|
| `pingTimeout` | `false` | `number` | `15000` | A heartbeat is sent every 15 seconds (15000ms). If any backend messages are received, the timer will reset |
| `pongTimeout` | `false` | `number` | `10000` | After the `ping` message is sent, the connection will be disconnected without receiving the backend message within 10 seconds (10000ms) |
| `reconnectTimeout` | `false` | `number` | `2000` | The interval for reconnecting |
| `pingMsg` | `false` | `any` | `"heartbeat" / ()=>"heartbeat"`| `ping` message value |
| `repeatLimit` | `false` | `number` | `null` | The amount of times to attempt reconnection default: `unlimited` |

```javascript
const options = {
    url: 'ws://xxxx',
    pingTimeout: 15000, 
    pongTimeout: 10000, 
    reconnectTimeout: 2000,
    pingMsg: "heartbeat"
}

let websocketHeartbeatJs = new WebsocketHeartbeatJs(options);
```

#### **`websocketHeartbeatJs.send(msg)`**
(function)

Sends the message to the back-end service

```javascript
websocketHeartbeatJs.send('Hello server');
```

#### **`websocketHeartbeatJs.close()`**
(function)

The front-end end manually disconnects the `WebSocket` connection. This method does not trigger reconnection.

## **Hooking events / functions**

#### `websocketHeartbeatJs.onclose` 
(function)

```javascript

websocketHeartbeatJs.onclose = (e) => {
    console.log('STATUS: Connection Closed');
}

```

#### **`websocketHeartbeatJs.onerror`**
(function)

```javascript
websocketHeartbeatJs.onerror = (e) => {
    console.log('connect onerror');
}
```

#### **`websocketHeartbeatJs.onopen`**
(function)

```javascript
websocketHeartbeatJs.onopen = (e) => {
    console.log('open success');
}
```

#### **`websocketHeartbeatJs.onmessage`**
(function)

```javascript
websocketHeartbeatJs.onmessage = (e) => {
    console.log('msg:', e.data);
}
```

#### **`websocketHeartbeatJs.onreconnect`**
(function)

```javascript
websocketHeartbeatJs.onreconnect = (e) => {
    console.log('Reconnecting...');
}
```

## **Demo**:
[demo show][2]


#### Blog
[初探和实现WebSocket心跳重连][3]



### **Similar utilities:**
[`websocket-heartbeat-miniprogram`][4]


  [1]: <https://github.com/zimv/websocket-heartbeat-js/blob/master/README-zh.md>
  [2]: <https://htmlpreview.github.io/?https://github.com/zimv/websocket-heartbeat-js/blob/master/demo/index.html>
  [3]: <https://www.cnblogs.com/1wen/p/5808276.html>
  [4]: <https://github.com/zimv/websocket-heartbeat-miniprogram>
