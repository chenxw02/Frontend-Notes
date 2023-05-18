# node.js + ws

## 引入

```js
const wsServer = require('websocket').server;
const ws = require('ws');
const http = require('http');
```

## 创建http和ws服务器

```js
// 创建HTTP服务器
const server = http.createServer((request, response) => {
  // 处理HTTP请求
});

// 创建WebSocket服务器
const ws_server = new wsServer({
  httpServer: server,
});
```

## 监听ws请求

使用on request，为每个请求连接的客户端创建一个新的ws实例，隔离各客户端之间的信息

```js
ws_server.on('request', (request) => {
  const connection = request.accept();
  const ws = {
    connection: connection,
    send: (message) => {
      connection.send(message);
    },
    on_message: (callback) => {
      connection.on('message', (message) => {
        callback(message.utf8Data);
      });
    },
  };

  handle_connection(ws);
    
  // 可在此分配各个ws实例私有的数据结构（属性），可在此ws实例的方法中访问
  let data = [];
});
```

## 处理ws连接

```js
const handle_connection = async (ws) => {
  ws.send('Welcome to Websocket!');

  ws.on_message((message) => {
    handle_message(ws, message);
  });
};
```

## 处理从前端收到的信息

```js
const handle_message = (ws, message_from_client) => {
  ws.send('Message Rceived!')
  console.log(`Received message from client: ${message_from_client}`);
  // 支持UTF-8内容 --可选
  const data_from_client = message_from_client.toString('utf8');
  // 转换为json(object) --可选
  const json_from_client = JSON.parse(data_from_client);
    
  // 在ws实例的方法内访问其属性
  ws.data.push(json_from_client);
};
```