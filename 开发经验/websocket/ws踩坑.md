# 使用node.js+ws过程中的踩坑记录

## 使用json通信，但需要传输jsonString

```js
const data_to_client = message_to_client.toString('utf8');
const json_to_client = JSON.parse(data_to_client);

// 发送
ws.send(JSON.stringfy(json_to_client))

// 接收
ws.on('message', (message_from_client) => {
    const data_from_client = message_from_client;
    const json_from_client = JSON.parse(data_from_client)
});
```



## 中文需要解码

如果需要使用ws传输中文字符，需要进行解码

```js
const data = message.toString('uft8');
```



## 每个通道只能有一个listener

不能无故新增listener，并且新增了listener后要取消很复杂（需要使用promise）

下面这段代码中，handle_message每次被执行都会新增一个listener，导致listener越来越多！

如果选在在listener后直接增加一个engine.off( 'message', listener )，则会由于同步执行的原因导致listener装载后立刻取消，无法达到监听效果！

```js
const handle_message = (ws, engine, message_from_client) => {
	if (engine) {
    	data = [];
    	console.log(`Received message from client: ${message_from_client}`);
    	const data_from_client = message_from_client.toString('utf8');
    	const json_from_client = JSON.parse(data_from_client);
        engine.send(JSON.stringfy(json_from_client));
        engine.on('message', (message_from_engine) => {
            console.log(`Received message from engine: ${message_from_engine}`)
        });
        // engine.off('message');
  	} else {
    	ws.send('engine is not connected');
  	}
};
```

## 对收到的message进行操作必须放在listener里

如果需要将message的数据存储到data里，由于异步的关系，在外部访问data时只能访问到存储之前的数据。

```js
let data = [];

engine.on('message', (message_from_client) => {
	console.log(`Received message from engine: ${message_from_client}`);
	const data_from_client = message_from_engine.toString('utf8');
	const json_from_client = JSON.parse(data_from_client);
	data.push(json_from_client);
	consloe.log(data);
});
```



