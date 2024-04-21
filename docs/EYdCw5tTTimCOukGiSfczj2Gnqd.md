# DIN：全程白嫖 - 拥有一个 AI 大模型的微信助手

> 来源：[`waytoagi.feishu.cn/docx/TQxQdzumToejbVxc0O4ceUwYnwg`](https://waytoagi.feishu.cn/docx/TQxQdzumToejbVxc0O4ceUwYnwg)

作者：丁先生

原文：

## 这有什么用？

一个能帮你解答任何问题的 AI 机器人，你可以将其接入到微信或群聊中，为用户提供自动答疑服务。你还可以投喂它特定知识，成为你的客服、专业老师或知识备忘录。

搭建内容：

完成上面 3 步就算 OK 了，那我们正式开始。

## 准备工作

### 领免费云服务器

### 领免费大模型接口

## 搭建 FastGpt、OneAPI

我之前申请了一台免费的腾讯云，下面我就在腾讯云的服务器上操作，阿里云的菜单位置可能不太一样，对应的找下就行，操作步骤完成一样哦。

![](img/7467e9bbabd50f2c1ab4211be0093094.png)

![](img/d887f6b66e1d42407301059138708460.png)

![](img/d44c5217e3019be664dbaf433e737738.png)

![](img/815f9912f5527dd98438865d377a0bfc.png)

![](img/b35826678c61fc74ce2168bd632add21.png)

有时候网络的问题，可手动创建一个 fastgpt 目录，并下载 2 个链接的文件，手动上传上去也行的（看截图中有“新建”“上传”按钮，可手动完成）

![](img/8f3053d141f14b7f8ed0680e157f2497.png)

执行成功会显示如下图。首次执行 docker-compose pull 可能会需要点时间，他会下载“mongo”“pg”“fastgpt”这几个项目。

![](img/984c301720b955795554c2a61c4e91b3.png)

![](img/ee890a436e5c30a9729bb17c7c007463.png)

![](img/7de89bd16394291675c2cf27349e5e74.png)

## 配置 FastGpt、OneAPI

![](img/7f10615a6b3319f3143534dfe53ccfd3.png)

添加时，类型选择阿里通义千问，名称自己取个，类型选择好后模型是会默认加进去，你不用删减，还有就把刚刚阿里那复制的 ApiKey 粘贴到秘钥里去。这样就 OK 了。后续有其他的大模型也是一样的添加方式。

![](img/ac1efc73e16fc175b7e02579a826f0c9.png)

![](img/3112bbd4fc8cfcb64bd46745e2ed26a9.png)

这里名称可以自己取个，时间设为永不过期、额度设为无限额度。提交 OK。

![](img/63adce5339cdb080eda160e3f7e076fb.png)

这时【令牌】中就有你添加的了，点击复制，得到令牌。

![](img/efcd7f2828fe60b2e93d8c813cf67313.png)

![](img/97f23e1a7343d01a381e7849cdb450f4.png)

![](img/dfb7a3c22e002d9120862e5ddbe97ff7.png)

![](img/76c253093733d0e315efc28a53d4f741.png)

![](img/a4cbfe5d73a1513966d2dc662ed84ec3.png)

![](img/4051db8708dd5f8e5408e868324f8790.png)

那我想根据我自己的知识库内容来回答问题，要怎么操作？

在知识库这个菜单新建知识库，看到新建时有 2 个模型选项了吧，在刚刚配置 config.json 时，我提到向量模型，就是这里的索引模型的选项，没有他知识库功能用不了哦。如图

![](img/a7a5423281ab0a9b7df3e2459d6750b5.png)

然后我们上传文件或者写入信息都是可以的，我示范下，最后文本状态是“已就绪”就是 OK 了，我们去验证下

![](img/d565f25843774f0e8cac93297f3a649a.png)

![](img/f835c3b764d33925b74ab853054972f3.png)

![](img/8d14515696e415b1de2e9b3aaaac7695.png)

![](img/50d2034dfffb4d7267ce7bbec2275e1c.png)

![](img/4fd94c8412700284020eb0791a0444cb.png)

![](img/8458d5807d4a70b09a5df1eee7c38cca.png)

![](img/9f8898850d5a2bca78e2ad599e645d3a.png)

## 搭建 chatgpt-on-wechat 接入微信

![](img/8ce96233604ae43e00dc959c4965925a.png)

注: 如遇到网络问题，可把链接替换为国内镜像 https://gitee.com/zhayujie/chatgpt-on-wechat

能够使用 itchat 创建机器人，并具有文字交流功能所需的最小依赖集合。

![](img/9d0ecb6a508022b6dcc51661a96837b9.png)

如果某项依赖安装失败，可重新执行再继续

![](img/9b8a21b6b738380741d310660da7dd40.png)

返回扫描执行命令后，“终端”内的二维码，就完成登录成功了。

![](img/ee1389008d81d8a87b751cfe1a7b6209.png)

![](img/55fc479f7a30604b4486f5860506d758.png)