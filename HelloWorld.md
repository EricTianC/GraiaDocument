# 历史性的第一次对话


现在我们需要协定好` mirai-api-http` 的配置，以便于接下来的说明.

打开mirai-api-http的配置文件，一般为`<Mirai Console>/config/net.mamoe.mirai-api-http`下的`setting.yml`文件，编辑以下项：

```yaml
authKey: my-graia-authkey   # 你可以自己设定，这里作为示范

# 可选，缓存大小，默认 4096. 缓存过小会导致引用回复与撤回消息失败
cacheSize: 4096

enableWebsocket: true   # 是否启用 websocket 方式，若使用 websocket 方式交互会得到更好的性能
host: '0.0.0.0'   # http-api 服务监听的地址，错误的设置会造成 Graia Application 无法与其交互
port: 8080   # http-api 服务监听的端口，错误的设置会造成 Graia Application 无法与其交互
```

完成后启动`Mirai Console`

将以下代码保存到文件 `bot.py` 内并替换, 确保该文件位于你的工作区内:

``` python
from graia.broadcast import Broadcast
from graia.application import GraiaMiraiApplication, Session
from graia.application.message.chain import MessageChain
import asyncio

from graia.application.message.elements.internal import Plain
from graia.application.friend import Friend

loop = asyncio.get_event_loop()

bcc = Broadcast(loop=loop)
app = GraiaMiraiApplication(
    broadcast=bcc,
    connect_info=Session(
        host="http://localhost:8080", # 填入 httpapi 服务运行的地址
        authKey="my-graia-authkey", # 填入 authKey
        account=8213231824, # 你的机器人的 qq 号
        websocket=True # Graia 已经可以根据所配置的消息接收的方式来保证消息接收部分的正常运作.
    )
)

@bcc.receiver("FriendMessage")
async def friend_message_listener(app: GraiaMiraiApplication, friend: Friend):
    await app.sendFriendMessage(friend, MessageChain.create([
        Plain("Hello, World!")
    ]))

app.launch_blocking()
```


运行代码, 终端输出:

``` bash
[root@localhost] $ python bot.py
[2020-07-25 21:42:11,929][INFO]: launching app...
[2020-07-25 21:42:11,960][INFO]: using websocket to receive event
[2020-07-25 21:42:11,964][INFO]: event reveiver running...
```

然后和机器人账号发起好友对话, 当你的机器人向你发出 `Hello, World!` 时,
你就已经部署好了一个最小的 `Graia Framework` 应用, 在接下来的文档中, 我们将开始介绍我们提供的各种 API 和特性.

