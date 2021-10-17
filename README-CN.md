# Telegram Bot API 库! [![GoDoc](https://godoc.org/github.com/MSCxar-0293/go-tgbot?status.png)](http://godoc.org/github.com/MSCxar-0293/go-tgbot)

|||                                             |---|---|                                       |[CN](README-CN.md)|[EN](README.md)|

> 来自一个最后更新于三年前的项目，先在这里感谢感恩原作者（

> 大概先做个汉化罢。

> 最后的列表懒得翻译了，就酱吧>vO

这是一种优雅地用GoLang来搭建tgbot的方法。

这里已经有几乎所有的功能了，并且很快所有功能将变得可用！如果你想要某个还未被加入进去的功能或者这些东西里有哪里坏了，那么去开个issue，然后让我们一起来修吧~

并且，如果你用这些开发了一只姬气人，我会很乐意知道哒~

## 免责声明

这是我第一次写GoLang的代码，所以，如果你看见了屎山，请一定告诉我，我将虚心求教！:-)

并且，一些人已经告诉我这个库里处理功能的方式太js化了。我正在尽可能尝试让它长得更像GoLang写出来的东西，我建立库之间联系基于'mux'和'gorequest'，如果你知道更好的、GoLang化的解决方法，请！告诉我！（我恨js，听到我的库长得像js让我很悲伤xD）

你可以通过 [telegram](https://telegram.me/rockneurotiko) 联系到我。（译者注：这是原作者tg号）

## 例子

`Show me the code!`

```go
package main

import (
	"fmt"

	"github.com/rockneurotiko/go-tgbot"
)

func echoHandler(bot tgbot.TgBot, msg tgbot.Message, vals []string, kvals map[string]string) *string {
	newmsg := fmt.Sprintf("[Echoed]: %s", vals[1])
	return &newmsg
}

func main() {
	bot := tgbot.NewTgBot("token").
		CommandFn(`echo (.+)`, echoHandler)
	bot.SimpleStart()
}
```

你可以在 [the echo example](https://github.com/MSCxar-0293/go-tgbot/blob/master/example/echoexample/main.go) 里看到这些例子。

@buldo 用 webHook 制作了一个部署在 heroku 中的机器人的实例，你可以在[这里](https://github.com/buldo/echo-go-tg-bot)看到它。

## 安装

你可以用`go get`这一工具安装所有的GoLang的库。

```
go get -u github.com/rockneurotiko/go-tgbot
```

## 收信息！

首先，你需要创建一个新的TgBot：
```go
bot := tgbot.NewTgBot("token")
```

`"token"` 是你在 [@botfather](https://telegram.me/botfather) 那里创建新TgBot时给你的那个token。

在那之后，你可以添加一些可以被执行的功能。下面就是一些功能的不同的方法和配置，你可以在里面选个适合的。这个功能会在goroutine里被唤起，所以不用担心“挂”bot。（译者注：？？我不会翻我太菜了）

### 回应文字消息

当前，有两个方法可以被使用：

- 最简单的：这可以用来回应没有参数的消息，比如`/help`。
  ```go
  func(TgBot, Message, string) *string
  ```
  以上的几个参数分别是：
  - TgBot：bot的实例，这样你就可以在那里面唤起功能。
  - Message：代表它的Message结构，这样你就可以获得任何参数。 
  - string：string里的消息。 :)

- 复杂一些：这应当在你使用正则时被使用

  ```go
  func(TgBot, Message, []string, map[string]string) *string
  ```
  以上的几个参数分别是：
  - TgBot：bot的实例，这样你就可以在那里面唤起功能。
  - Message：代表它的Message结构，这样你就可以获得任何参数。
  - []string: 这是正则表达式中的捕获组，很容易得到它们 ^^
  - map[string]string: 这是命名的捕获组，更容易获得它们！！

使用这两种函数，您可以在`TgBot` 实例中使用命令构建函数调用，所有以`Simple` 开头的函数都使用最简单的函数调用。

在解释它们之前，您必须知道什么是命令。 命令是 Telegram API 理解的命令，[@botfather](https://telegram.me/botfather) 允许您使用 `/setcommands` 函数定义的命令。

这些命令总是看起来像 `/<command>`，但它们可以有其他参数 `/<command> <param1> <param2> ...`，我们稍后会看到。

奇怪的是，命令可以被称为`/<command>` 或`/<command>@username`，当你在一个组中并且你想指定机器人来发送该命令时，这很有用。 如果你使用这个函数，你不必担心添加或处理@username，库会为你神奇地处理它 <3

此外，更神奇的是你不需要写 `/` 命令，也不需要写表达式的安全命令字符，即开始的 `^` 和前导的 `$`，所以，如果你说你想要 命令`help`，库会理解你并制作`^/help(?:@username)?$` :)

所以，先别说了，让我们看看你可以使用的功能，在[这个简单的示例文件](https://github.com/MSCxar-0293/go-tgbot/blob/master/example/simpleexample/main.go)中你可以 看到人人都能掌握的例子 ^^

- `SimpleCommandFn(string, func(TgBot, Message, string) *string)`:
  这是一个基本的例子。当你想要一个没有参数的命令时（比如基本的 `/start` 或 `/help`），你只需要创建一个我们之前看到的 `simple` 函数，并将其添加到命令中。 例如，这段代码会在 `/help` 命令中添加一个简单的命令处理程序（`helpHandler`），它会被一个 `/help` 命令和 `/help@username` 正确调用 ^^
  ```go
  bot := tgbot.NewTgBot("token").
    SimpleCommandFn(`^/help$`, helpHandler)
  bot.SimpleStart()
  ```

- `CommandFn(string, func(TgBot, Message, []string, map[string]string) *string)`:
  有时，你不想要一个简单的命令，你想要一些更有趣的带参数的东西，那么这个功能可以帮助你。
  这段代码将向`/echo`命令添加一个命令处理程序（不是那种简单的）（`echoHandler`），该命令将接受一个参数，如果使用`/echo@username`，它将被正确调用~
  ```go
  bot := tgbot.NewTgBot("token").
    CommandFn(`^/echo (.+)`, echoHandler)
  bot.SimpleStart()
  ```
  echoHandler 中的 `vals []string` 参数的大小为 2，第一个是完整的文本，第二个是捕获组 `(.+)` 处理的内容。

- `MultiCommandFn([]string, func(TgBot, Message, []string, map[string]string) *string)`:
  其他时候，您需要多个命令，例如，您需要一个 `/help` 和一个 `/help <command>`。当然，您可以构建一个与之匹配的正则表达式，或者使用两个不同的函数构建它，但是，也许会有更复杂的情况呢？那么这就是构建它的一种优雅的解决方式。
  您只需给他一个正则表达式列表，这些表达式将尝试按顺序执行，但只会执行其中一个。
  ```go
  bot := tgbot.NewTgBot("token").
    MultiCommandFn([]string{`^/help (\w+)$`, `^/help$`}, helpHand).
  bot.SimpleStart()
  ```

这就是处理命令的所有功能！ 你构建他们时将有很多选择，而且很多时候它们是可以互换的，所以你可以使用你喜欢的那个=D

看完命令的自定义方法后，让我们看点其他的。比如说，您可能会想处理一些不是命令的消息。于是乎——正则表达式函数来拯救你辣！

那么，为什么要使用不同的函数呢？ 两个原因：第一个，你可以知道什么是“真正的”命令，什么不是，只为在调用中查找；第二个，因为命令函数中自动
 处理`@username` 的神奇功能，在自定义正则表达式中无法正常工作 ;-)

实际上，这个函数的工作方式与它们的类似命令的函数完全一样，只是不会像普通指令一样自动添加 `@username` 而已 :)

- `SimpleRegexFn(string, func(TgBot, Message, string) *string)`:
  ```go
  bot := tgbot.NewTgBot("token").
    SimpleRegexFn(`^Hello!$`, helloHand)
  bot.SimpleStart()
  ```

- `RegexFn([]string, func(TgBot, Message, []string, map[string]string) *string)`:
  ```go
  bot := tgbot.NewTgBot("token").
    RegexFn(`^Repet this: (.+)$`, repeatHand)
  bot.SimpleStart()
  ```

- `MultiRegexFn([]string, func(TgBot, Message, []string, map[string]string) *string)`:
  （对不起，这是个不怎么好的例子，但我没有想到怎样才能更好地举例。如果你有一些更好的例子，请告诉我！）
  ```go
  bot := tgbot.NewTgBot("token").
    MultiRegexFn([]string{`^First regex$`, `^Second regex (.*)`}, multiregHand)
  bot.SimpleStart()
  ```


### 调用文件信息

- `ImageFn(func(TgBot, Message, []PhotoSize, string))`
  接收图像时调用的函数，两个额外参数是 PhotoSize 的数组和文件 ID。

### 其他杂项调用

- `AnyMsgFn(func(TgBot, Message))`
  This functions will be called in every message, be careful!

- `CustomFn(func(TgBot, Message) bool, func(TgBot, Message))`
  使用此回调，您可以添加自定义条件，首先它将执行第一个函数，如果返回值为 true，则执行第二个。


## 做点回应

所以，您知道如何在收到消息时调用您的函数，但是您如何回答呢？ 那真的很重要！ 如果机器人不能回答，那么这个机器人将没有弔用！ 那么，让我们来谈谈 TgBot 中可用的动作功能。

 一个基本的动作就是发送文本（这真的很简单），你看到这些函数最后有一个 `*string` 参数吗？ 嗯，那是因为您返回的字符串将被发送到消息来自的聊天。 简单！为什么用指针？因为它允许返回`nil`，但我不喜欢这样，如果您有更好的主意，请告诉我！

例如，此函数将向发送者（个人或组）发送相同的消息：
```go
func example(bot tgbot.TgBot, msg tgbot.Message, text string) *string {
    return &text
}
```

你也可以做其他回应！ 你看到第一个参数是 TgBot 实例了吗？ 那让你可以做回应！

所有的动作都有一个“纯发送查询”的函数，你可以直接调用它们，它们被称为`ActionNameQuery`，例如，`SendMessageQuery`或`ForwardMessageQuery`，
 但最好使用自定义函数：

### Message actions

- `SendMessage` 函数:

    - `SimpleSendMessage(msg Message, text string) (Message, error)`: 使用消息和字符串简化调用，它将将该字符串发送给发送者。

    - `SendMessage(chatid int, text string, disable_web_preview *bool, reply_to_message_id *int, reply_markup *ReplyMarkupInt) ResultWithMessage`: 发送包含所有参数的消息、聊天 ID（您可以使用 msg.Chat.ID 访问）；要发送的字符串和两个指针（因为是可选的，所以如果您不想要它们，只需传递 `nil`）；disable\_web\_preview、reply\_to\_message\_id和reply\_markup（这是一个接口，你可以使用的结构体有：`ReplyKeyboardMarkup`、`ReplyKeyboardHide`和`ForceReply`，但对于这个，最好使用下面的那个功能）。

  - `SendMessageWithKeyboard(chatid int, text string, disable_web_preview *bool, reply_to_message_id *int, reply_markup ReplyKeyboardMarkup) ResultWithMessage`: 这使得发送键盘更容易，只需传递结构 :)

  - `SendMessageWithKeyboardHide(chatid int, text string, disable_web_preview *bool, reply_to_message_id *int, reply_markup ReplyKeyboardHide) ResultWithMessage`: This makes easier to send a the hide keyboard, just pass the struct :)

  - `SendMessageWithForceReply(chatid int, text string, disable_web_preview *bool, reply_to_message_id *int, reply_markup ForceReply) ResultWithMessage`: 这使得发送强制回复更容易，只需传递结构:)

  - `SendMessageQuery(payload QuerySendMessage) ResultWithMessage`: 尽 量 别 用 这 个 :)


- `ForwardMessage` 函数:

  - `ForwardMessage(chatid int, fromid int, messageid int) ResultWithMessage`: 将转发到 `chatid` 那些来自 `fromid` 的消息

  - `ForwardMessageQuery(payload ForwardMessageQuery) ResultWithMessage`: 尽 量 别 用 这 个 :)

### 文件操作

- `SendPhoto` 函数，无论你在哪里看到 `path`，（可以是文件路径或文件 id），它会自动处理：

  - `SimpleSendPhoto(msg Message, path string) (Message, error)`: 使用路径和字符串的简化调用，并且它可以将该字符串发送给发送者。

  - `SendPhoto(chatid int, path string, caption *string, reply_to_message_id *int, reply_markup *ReplyMarkupInt) ResultWithMessage`: 与 SendMessage 类似，只是是发送照片用的。使用它可以完全控制参数。

  - `SendPhotoWithKeyboard(chatid int, path string, caption *string, reply_to_message_id *int, reply_markup ReplyKeyboardMarkup) ResultWithMessage`: 这使得发送键盘更容易，只需传递结构而不是指向接口的指针 :)

  - `SendPhotoWithKeyboardHide(chatid int, path string, caption *string, reply_to_message_id *int, reply_markup ReplyKeyboardHide) ResultWithMessage`: 这使得发送隐藏键盘更容易，只需传递结构而不是指向接口的指针 :)

  - `SendPhotoWithForceReply(chatid int, path string, caption *string, reply_to_message_id *int, reply_markup ForceReply) ResultWithMessage`: 这使得发送强制回复更容易，只需传递结构而不是指向接口的指针 :)

  - `SendPhotoQuery(payload interface{}) ResultWithMessage`: 尽 量 别 用 这 个 :) (顺便说一句，接口{}双次检查 SendPhotoIDQuery 和 SendPhotoPathQuery)


## 所有的例子！

您可以在[simpleexample/main.go file](https://github.com/mscxar-0293/go-tgbot/blob/master/example/simpleexample/main.go)中找到包含所有函数/调用的完整示例。

如果你想自己处理消息，也不是不可以，你必须向侦听器添加添加一个通道并启动它。

看看[manualexample/main.go file](https://github.com/mscxar-0293/go-tgbot/blob/master/example/manualexample/main.go)来了解了解自己处理消息的例子。

## 已完成的和未完成的...

欢迎您帮助构建这个项目 <3

- [x] 回调函数
  - [x] 简单指令
  - [x] 带参数的命令
  - [x] 多个命令
  - [x] 简单的正则表达式
  - [x] 普通正则表达式
  - [x] 多个正则表达式
  - [x] 任何消息
  - [x] 自定义函数
  - [x] 图片消息
  - [x] 音频消息 
  - [x] 文档消息 
  - [x] 视频消息 
  - [x] 位置消息
  - [x] 回复消息
  - [x] 消息转发
  - [x] 任何群组事件
  - [x] 进群事件
  - [x] 退群事件
  - [x] 更改群组标题
  - [x] 更改群组图片
  - [x] 删除群组图片
  - [x] 群组创建事件

- [x] 操作函数
  - [x] 获取我
  - [x] 发送信息
    - [x] 发送键盘的简单方法
    - [x] 强制回复的简单方法
    - [x] 隐藏键盘的简单方法
  - [x] 转发信息
  - [x] 获取更新
    - [x] This is done automatically when you use the 当你使用`SimpleStart()` 或 `Start()`的时候，you shouldn't touch this ;-)
  - [x] setWebhook  (!! in testing !!)
    - [x] This is done automatically when you
      use `ServerStart()`, you can use other
      ways.
  - [x] Send photo
   - [x] From id
   - [x] From file
   - [x] easy with keyboard
   - [x] easy with force reply
   - [x] easy with keyboard hide
  - [x] Send audio
   - [x] From id
   - [x] From file
   - [x] easy with keyboard
   - [x] easy with force reply
   - [x] easy with keyboard hide
  - [x] Send document
   - [x] From id
   - [x] From file
   - [x] easy with keyboard
   - [x] easy with force reply
   - [x] easy with keyboard hide
  - [x] Send sticker
   - [x] From id
   - [x] From file
   - [x] easy with keyboard
   - [x] easy with force reply
   - [x] easy with keyboard hide
  - [x] Send video
   - [x] From id
   - [x] From file
   - [x] easy with keyboard
   - [x] easy with force reply
   - [x] easy with keyboard hide
  - [x] Send location
  - [x] Send chat action
  - [x] Get user profile photos

- [ ] Other nice things!
  - [x] Default options for messages configured before start.
    - [x] Disable webpage preview
    - [x] Selective the reply_markup
    - [x] One time keyboard
    - [x] Clean initial @username in message
    - [x] Add slash in message if don't exist and @username had been used
  - [ ] Easy to work with authorized users
  - [x] Easy to work with "flow" messages

- [ ] Complete documentation xD
  - [ ] Audio doc
  - [ ] Document doc
  - [ ] Sticker doc
  - [ ] Video doc
  - [ ] Location doc
  - [ ] ChatAction doc
  - [ ] Awesome chain doc
  - [ ] GetUserProfilePhotos
  - [ ] Webhook
  - [ ] Chain messages
  - [ ] Default options
  - [ ] Call from ReplyFn

- [ ] 测试


[![Bitdeli Badge](https://d2weczhvl823v0.cloudfront.net/rockneurotiko/go-tgbot/trend.png)](https://bitdeli.com/free "Bitdeli Badge")
