<!--yml
category: 商业
date: 2024-10-14 09:18:28
-->

# Getting Real by 37signals(转 37signals)

> 来源：[http://www.360doc.com/content/08/0510/12/8309_1249347.shtml](http://www.360doc.com/content/08/0510/12/8309_1249347.shtml)

* * *

## 引言 第一章

## 什么是 Getting Real?

想构建一个成功的Web应用么? 那么正是时候Getting Real. Getting Real 是一种更小规模，更快速，更高质量的软件构建方法。

*   Getting Real是关于省略所有表达现实（图表，曲线，矩形，箭头，统计图），而构建现实。
*   Getting real 是追求精炼。更少的代码量，更少的软件，更少的功能，更少的文档工作，更少无所谓的东西（而且大部分你认为必要的，其实不是）。
*   Getting Real 是保持精益，变得敏捷。
*   Getting Real从界面开始，也就是用户使用的屏幕。它从实际的用户体验开始，并且构建似曾相识的体验。这让你在软件误入歧途之前得到正确的用户界面。
*   Getting Real 是关于迭代和降低变化成本的方法。Getting Real基本上是关于上线，调整，持续改进，其目标的开发Web软件的最佳途径。
*   Getting Real只交付客户所需的，摒弃任何客户不需要的。

## Getting Real的优点

Getting Real能够交付更好的结果，是因为它强迫你处理真正要解决的问题，而不是关于那些问题的空想。它迫使你面对当下。

Getting Real更注重实际的用户界面，而不是功能规格说明书和其他昙花一现的文档。只有当一个真实的网页呈现出来，相关的功能规格才是可信的，被证明是可接受的。那才是是我们的客户将要看到和使用的。那才是需要关心的。Getting Real帮助你更快达到这个目的。并且那意味着你正在基于真实需求，而不是异想天开来构建软件。

最后，Getting Real是适合于Web软件的理想途径。那种把软件包装在盒子里，再等一年到两年才发布一个更新的学院派方法已经过时了。不像需要安装的软件，Web应用能够以天为单位持续改进。Getting Real利用了这种优势来提升Web应用的价值。

### 如何编写健壮的软件 How To Write Vigorous Software

健壮的著作是简明的。句子无废词，段落无废句子。同样的原因，画应无多余的线条，机器应无多余的零件。这不是要作者刻意缩句来逃避细节，从而提纲挈领，而是要作者字字珠玑。

—来自 ["The Elements of Style"](http://www./141/strunk5.html) by William Strunk Jr.

## 不再发胖

旧方式：冗长，官僚主义的，“我们正在这么做来控制这些蠢驴” 的流程。典型后果是：臃肿，过目即忘，平庸得掉渣的软件。Blech。

## Getting Real 除掉...

*   花费数月，甚至数年的进度表
*   不切实际的功能规格文档
*   可伸缩性的争论
*   又臭又长的员工大会
*   大量招人的需求
*   毫无意义的版本号
*   憧憬完美未来的幼稚“路线图”
*   无穷尽的偏好设置选项
*   外包支持
*   不切实际的用户测试
*   写无用文档
*   自顶向下的管理结构

你不需要成吨的钞票或者庞大的团队或者漫长的开发周期来构建伟大的软件。那些正是缓慢，晦涩，变化成本高昂的应用程序的帮凶。Getting real反其道而行之。

## 这本书将带给你...

*   信仰之重要
*   为什么小是好事情
*   怎样构建更少
*   怎样从现实世界中快速找到创意
*   怎样培养团队
*   为何要由内到外的设计
*   为什么写作至关重要
*   为什么要比对手少做
*   如何升级你的应用和散播文字
*   成功维护的秘诀
*   发布后能够持续保持后劲的秘诀。
*   其他...

本书关注于理论高度。我们不会使你陷入代码片段细节，或者是CSS窍门。我们会坚守在驱动Getting Real过程的主要思想和价值观上。

## 本书适合你么？

你是管理者，设计师，程序员，或者市场人员。

你意识到旧规则不再管用了。每年通过光盘分发你的软件？2002这个版本号怎么样？

或者你对敏捷开发和企业组织结构略懂皮毛，但是热切的想多了解一些。

**如果听起来你是其中之一，那么这本书就是为你准备的。**

注意：虽然这本书着重在构建Web应用上，很多理念也可以应用到非软件活动。关于小型团队，快速原型，期望迭代，和许多提到的其他经验能够为你引路。无论你正在开始一项业务，写一本书，设计一个网站,记录签名册，还是其他各种各样的活动。一旦你在你生活中的某一领域开始Getting Real，你就或发现这些概念能适用的非常广泛。

* * *

## 关于 37signals

## 我们做什么

37signals是一个创造简单的，专一的软件的小团队。我们的产品帮助你协同工作，组织团队。超过35000个人和企业使用我们的Web应用来搞定他们的业务。来自华尔街杂志的Jeremy Wagstaff写到 “37signals 的产品是超简单，精致，直接了当的工具，这些工具让微软的Outlook软件使用起来像受刑 。”我们的软件不会把你推到这种境地。

## 我们的习惯做法

我们相信软件太复杂了。太多的功能，太多的按钮，需要学习太多东西。 我们的产品比对手做的少 — 故意地. 我们构建的产品运行灵巧，感觉舒适，允许你以自己的方式做事，并且容易使用。

## 我们的产品

当这本书出版之即，我们有5个商业产品和一个开源框架。

[Basecamp](http:///) 把项目管理作为首要问题。Basecamp提供了消息板，待办事宜，简单调度，协同写作，文件共享。 而不是甘特图，炫丽的曲线图，和繁重的电子表格。目前，成千上万的人同意这是一种更好的方式。来自Salon.com的Farhad Manjoo说：“Basecamp代表了Web软件的未来。”

[Campfire](http:///) 提供了业务模式下的简单群聊方式。实时持久的群聊对于业务来说非常重要。传统的实时聊天对于快速的一对一模式很有效。但是对于3个或者更多的人同时聊天来说异常痛苦。Campfire解决了此问题和其他相关问题。

[Backpack](http:///) 是一种替代那些玄乎，复杂，“通过25个步骤管理人生”之类的个人信息管理系统的产品。Backpack在页面，笔记，待办事宜，电话和电子邮件通知上的简单尝试，在受“statis-quo-itie”折磨的一类产品中，是一个独具匠心的创意。Wall Street Journal的Thomas Weber说它是同类产品中最出众的。 New York Times 的 David Pogue说它是一个“非常酷”的组织工具。

[Writeboard](http://www./) 使你能够撰写，分享，修订，和比较自己或者他人的文章。臃肿的文本处理工具，对于你95%的文字是功能过剩的，而Writeboard是一个全新的替代品。Web-guru Jeffrey Zeldman说：“37signals 的天才思想王者归来。”

[Ta-da List](http:///) 维护聚合你的所有待办清单，并且以在线方式组织。为你自己维护待办清单，或者通过和其他人分享来协作。没有更好的方式来搞定这些了。迄今为止，其创建了超过100，000个清单和1，000，000项行动。

[Ruby on Rails](http://www./), 对于开发者来说，是一个用Ruby编写的全栈式的开源Web框架。其使得开发真是应用快速而简单。你可以关注在你的思想上面，而由Rails操心杂事。O’Reilly的Nathan Torkington说：“Ruby on Rails太令人震撼了。使用它像是观赏一个功夫片，片中一堆流氓框架准备痛扁这个小新人，没想到却被各种充满想象力的方式揪住了屁股。”Gotta喜欢这段话。

你可以从[www.](http://www./)找到更多关于我们产品和我们的公司的信息.

* * *

## 告诫，免责，和其他丑话说在前头

为了扫清障碍，下面是我们对于一些不时听到的抱怨的答复。:

## "这些技术不适合我"

Getting real 是一套对我们来说效果非凡的系统。但是本书的思想并不是放之四海皆准。如果你正在构建一套武器系统，一个导弹控制设备，一个为数以百万用户服务的银行系统，或者其他对生命、财产至关重要的系统，你将会回避一些我们的放纵主义态度。请继续前行并且采取一些其他的防范措施。

而且不必全部采纳或者全盘否定我们的主张。即使你没能力完全Getting Real，一定有少许观点你能够偷偷摸摸避开当权者而实行。

## "这些思想不是你们发明的"

我们没有声明我们发明了这些技术。许多概念已经以各种形式伴随我们很久了。当你读到一些我们的建议，并且它提醒你读到的一些东西已经在一些人的日记或者一些已经出版了20年的书 1cc;面了。这是完全可能的。这些技术并不是37signals的独创。我们只是告诉你我们怎样工作和是什么带给我们成功的。

## "你们的观点过于绝对"

如果我们的口吻看起来好像无所不知，目中无人，请宽容我们。我们认为果敢地提出观点要比唯唯诺诺，模棱两可要好得多。如果这是骄傲自大的形象，它就是。我们宁愿具有煽动性，也不愿意用“那要看…”这样的话来和稀泥。当然这些规则需要时间来完善或者打破。而且一些策略可能不适合你的场合。请运用你的判断力和想象力。

## "我们公司内部不适用."

觉得你的公司太大以至于难以Get Real？连微软也Getting Real（而且我怀疑你的公司更大）。

即使你的公司典型地执行长期，大型团队的调度计划，仍有方式Get real。第一步是分解成更小的团队。当太多的人牵扯进来，什么事都搞不定。你越轻装上阵，事情就做的越快越好。

没错，这需要一些推销潜质。让你们的公司投身于Getting Real过程中。给他们出示这本书。向他们炫耀你用更少时间和更小团队所得到的真实成就。

解释Getting Real是一种尝试新概念的低风险，低投入的方式。

如果你有勇气的话，秘密行动。在雷达下面飞行来证明真实结果。这正是Start.com团队在微软运用Getting Real的方式。“我观察过Start.com团队的工作方式，而他们没有经过许可”，微软的技术传播者Robert Scoble说。“他们有一个老板作为空中掩护。他们每次只接受一点功能，实现他们，并且响应反馈。”

### 推进微软的 Start.com

在大公司中，流程和会议是非常平常的。数月的时间被浪费在规划功能，争论细节，力求达到每个人在什么是对于客户来说是“正确”的东西上达成一致。

这可能是塑料包装软件的正确途径，但是基于Web的软件有难以置信的优势。立刻发布！让用户告诉你这是不是正确的东西。如果你愿意，你可以当天修正它然后发布最新版。没有话比客户的意见更有用 — 拒绝举行罗嗦的会议和争论。仅仅发布产品，并且证明这个观点。

知易行难 — 这说明：

*数月的规划没有必要。*
花费数月来写规格说明根本没必要 — 规格说明应该在开发过程中理清框架，描述并且精化细节。不要试图在开发开始之前解决所有选而悬而未决的问题，并且敲定所有细节。

*发布更少，但高质量的功能.*
你不需要一道电闪雷鸣伴随着全新的发布和一捆新功能。一小块一小块地喂用户，让他们能够消化。

如果发现了细微的bug，先发布敲定的核心功能，然后发布补丁。动作越快用户反馈越好。纸上谈兵听起来不错，但是实践中往往不理想。你越快发现观点的关键错误越好。

一旦你快速迭代，并且响应用户反馈，你就和用户建立了一种关系。记住目标是通过构建用户所想来赢得客户。

—Sanaz Ahari, [Start.com](http://www./), [Microsoft](http://www.microsoft.com/) 的程序经理

* * *

## 起跑线 第二章

## 建构从简

## 做得比竟争对手少

常规的思维方式告诉我们，不管竞争对手做什么你总是要比他们加多一些。如果他们有4个特色功能，你就需要做出5个（或15个，或25个）。如果他们花了x，你就该花xx。如果他们有20，你就得有30。

这种强调更多一层的冷战竞争思维是行不通的死胡同。如此创造产品的方式是昂贵的，过分防御的，并且有点偏执不正常的。防御性的偏执的公司是做不到前瞻性思维的，他们只能做事后思维。他们不可能领导，只能跟从。

*如果你只想创建一个随大流的公司，那么你现在就可以放下这本书，无需继续读下去了。*

那怎样才是有效的方法呢？答案是：做少。靠做得比对方少来打败他。解决简单的问题，把繁复困难棘手的问题留给大众。不做更多，相反的我们做的更少。不赶超，相反的我们试着退一步，守后。

我们将会将贯穿全书论述“少做”的概念，现在先简要介绍“少做”的含义作为热身：

*   更少的功能
*   更少的选择项和首选项
*   更少的配备人员和企业架构
*   更少的会议和抽象讨论
*   更少的承诺

* * *

## 是什么问题一直困扰你？

## 为自己而做这个软件

一个很好的做软件的方式就是一开始用它来解决你自己的问题。由于你自己变成了软件的目标受众因此你会知道什么是重要的什么不是。这样做下去将会是推出一个突破性产品的伟大起始点。

关键是你要了解你并不孤单。如果你有一个困扰你的问题那么非常有可能成百上千的其他人业有同样的烦恼。这，就是你产品的市场。看，不难找到吧？

Basecamp这个产品来自于一个困扰我们的难题：做为一个设计公司，我们需要有一个很简单的方式来和客户做项目沟通。一开始，我们建了一个外部网，通过不断更新其内容来连线客户。但每次一个项目需要更新我们就得手动更改HTML，这实在不是一个解决方案。这些项目网站总是看起来不错但最终总是被放弃了。这是很令人恼火的，因为它使我们变得很没有组织性，也让客户感到无所适从。

于是我们开始寻找其他解决方案。但每样我们找到的工具都是 1)并不能做我们想做的 或 2)充斥着我们所不需要的功能 — 比如象账单，登录权限控件，图表，等等。我们觉得一定会有更好的方案，最终我们选择了自己来做这个软件

当你在做软件解决你自己问题的时候，你创造的工具是你对之有激情的。那激情就是关键所在。激情意味着你真正去用这个软件，去关心这个软件。这是能感动他人并一起为之所动的最好的方式。

### 自助（自挠其背而止痒）

这是很长一段时间以来被开源领域奉为箴言的 — 他们叫它做“自挠其背而止痒”。对开源软件开发者来说，这意味着他们将自己去组织必需的工具，用自己的方式来推出产品。不仅于此，这样做的有着更深远的裨益。

作为一个新软件的网页设计师和程序开发者，每天你要面对上百个细小的决策：是要蓝色的还是绿色的？用一个表格还是两个？静态的或是动态的页面？放弃重新来过还是修复？一般我们是怎样来做这些决定的呢？如果那是我们认为很重要的我们也许会提出来讨论。其余的我们就靠猜想。最终所有那些猜想的部分将积累成软件的一种债务 — 一堆互相交织着的错综复杂的充满不确定因素的网。

作为一个开发者，我厌恶这种现象。想到有那么多的小型定时炸弹分布在这个软件中，给我带来了很大的压力。 自己帮助自己的开源软件开发者不会有这样的困扰。因为他们就是用户本身，90％的情况下他们了解他们应该做什么决定。我想这是为什么这些人能够在一天辛苦的工作回家后还能继续编写开源程序的众多原因之一，因为它反而是一种放松。

—Dave Thomas, [一个实用主义编程者](http://www./)

### 一切源于必要性

Campaign Monitor公司的成立事实上是来源于必要性。多年来各种email市场营销工具的低劣品质一直困扰着我们。一个工具可以做到x和y却总也做不到z，另一个能搞定y和z却总也做不好x。老是这么下去我们将无法成就赢家。

我们于是决定整顿我们的时间表，着手开发我们理想中的email市场营销工具。我们很清醒地决定不看其他人在做什么而是专心做一个能让我们自己和客户都活得自在一些的产品出来。

事实证明，我们并不是惟一对现状感到不满的人。我们对软件成品做了一些改动，这么一来所有的设计公司（不仅我们自己）都能使用它并且开始帮我们推广。不到6个月，数以千计的设计师开始用Campaign Monitor来发布他们自己和客户的email推广信了。

—David Greiner, 创始人, [Campaign Monitor](http://www./)

### 你必须在乎这个东西

当你在写一本书，你必须要有一个以上的有趣故事可讲。你必须有强烈地要讲叙这个故事的欲望。你必须要以某种方式把自己投入到故事中去。如果你要和一件事业共存两年，三年或你地一生，你必须要真正在乎它

—Malcolm Gladwell, 作者 (from [杂看Malcolm Gladwell](http://www./authors/gladwell.html))

* * *

## 找自己募资

## 外部资金只是第二条路(plan B)

很多创业者的首要任务就是找投资者募集资金。但必须记住，当你寻求外部资金的时候就意味着你不得不向别人汇报。于是别人对你的期望值将提升。投资者无不希望他们能够收回资金 — 并且是以最快的速度收回。导致一个不幸的事实就是往往抢钱优先过做一个优质的产品。

现时创业并不需要太多的启动资金。硬件便宜了，大量的优秀框架软件也都开源免费了。而且创业的激情是不需要标价的。

因此手头有多少钱就先动起来做多少事。用心想想决定什么是你最基本需要的，什么是可以先舍弃不做的。什么事是你可以三个人就搞定而不必用到十个人？什么是两万块而不用十万块就能办到的？什么样的软件你可以一边白天打工用业余时间做出来的？

## 资源拮据往往能激发想象力

当在有限的资源平台上运作，你往往会被迫更早的更紧迫的认识到这种压力。那是一件好事。有压力才会促使创新。

拮据也能迫你更早地将你的点子推出来 —这是另一件好事。这么跨出去一两个月后你就会比较清楚的了解这个点子是否有戏。这样一来，你就会在短时间内树立起自信心，不再那么急切寻求外部资金了。如果你的点子行不通，是虚的（酸柠檬），那么就是重新来过的时候了。坏消息至少你现在知道比几个月后或几年后知道好。至少你比较容易退出。当有投资者涉入的时候退出方案要复杂的多了。

如果你做软件只是想搞一度快钱，那么事实是瞒不住的。想很快的回本实践中是不大可能的。所以，专心做一个优质的软件，做出一个你和你的客户都能长久依赖的工具才是正道。

### 两条路

[Jake Walker拥有的一家公司是用投资者的钱创立的 ([Disclive](http://www./)) 另有一家不是 ([The Show](http://www./))。 在这里他探讨了这两条路的不同风景。]

所有问题的根本并不是筹募资金本身，而是随之而来的一系列事情。基本上就是更高的期望值。人们于是开始领工资，然后动力就成了做一个软件然后把它卖了，或想办法把种子基金给还了。就我前面的第一家公司（Disclive)，出于必要性我们起步得比我们原先设想的高很多 —

[关于第二家公司The Show] 我们发现我们可以推出一个花更少的钱却可以做得更好得产品，只不过要多花些时间。我们把很多自己的钱赌到一个人们愿意牺牲一点时间来换取品质的产品上面。但公司一直保持着小规模（也预计会一直这么走下去）。从那以后，我们就全部是内部募资。通过采取一些灵活的交易方式和我们的供应商合作，我们从不需要投入自己很多的资金。并且我们并不是做大后卖了，而是随需要而发展，走持续的可盈利道路。

—评论来自于 [Signal vs. Noise](http://www./svn/archives2/entrepreneurs_angels_and_the_cost_of_launch.php)

* * *

## 固定时间和预算，但灵活控制产品外延

## 准时地在预算内推出

一个简单方法让你能准时地在预算范围内推出产品：定额定量。绝对不要在一个难题上多投时间和金钱。要么缩小规模，要么缩小范围。

总会有一个这样误区：我们能做到准时地，在预算内发布一个规模完整的产品。这可以说完全不可能的，如果真是这样的话一定是以质量做为代价的。

如果你不能在预定的时间和预算内完成所有的东西的话，就不要拉长时间和增加预算。相反的，把产品的外延缩小些。下一步总是有时间可以加东西进去 — 过后有的是时间，当下却是稍纵即逝的。

做一个比预计要小巧些的好东西比做一个庞大平庸而又漏洞百出的东西要现实的多，因为你要象魔术师一样巧妙的照顾到时间，预算和产品内容的方方面面。变魔术就交给Houdini（魔术大师）。你所做的可是在运作一个真正的事业，在推出一个真实的产品。

以下是一些固定时间和预算，灵活控制产品外延的好处:

*   #### 要有优先级

    你一定要搞明白什么才是最重要的。什么是首发的产品中必须要具备的特性功能？这个限制思维逼迫你下一些痛苦但必要的决定，而不是挑挑拣拣的拿不定主意。
*   #### 要现实些

    设定期望值是关键。如果你同时要固定死时间，预算和产品的外延，你将不能推出高层次的产品。当然你可能推出个东西，但那个“东西”会是你真正想做的吗？
*   #### 要有灵活性

    及时应变的能力很重要。如果什么都固定死就很难应变。给产品的外延注入机动性，当真正做起来就有比较多的选择空间。机动灵活是你的良师益友。

我们建议：范围缩小些。做半个产品比做半拉子的产品好（后面将进一步论述这点）。

### 一天，两天，三天...

知道一个项目是怎样拖到最后比预计迟一整年的吗？就是这么一天一天拖出来的。

—Fred Brooks, [软件工程师，电脑科学者](http://en./wiki/The_Mythical_Man-Month)

* * *

## 找个敌人

## 挑选一场战斗

有时了解你的应用程序应该做成什么样子的最佳方式就是，认识到它不应该成为什么。搞清楚你的软件的对手是谁，就象点一盏灯，能照亮你前行的道路。

当我们决定开发项目管理软件的时候，我们清楚的知道微软项目软件（Microsoft Project)是这个小舞台上的一个庞然大物，大猩猩。我们不是去害怕这个大家伙，相反的我们把它做为一个刺激我们前进的引擎。我们决定Basecamp将做成一个完全不同的项目管理软件，就是反Microsoft Project而行。

我们意识到项目管理并非就是有关图表，报告和统计数据 — 它更重要的是沟通。它并不是要一个项目经理坐得高高在上去传达一个项目计划。它应该是每个人都参与的，一起担起责任来使项目付诸实现。

我们的敌人就是项目的独裁者和他们用来指手画脚的工具。我们要把项目管理民主化 — 让每个人都能成为它的一份子（包括客户）。只有每个人都承担负责流程的一部分，这样整合起来项目才能做得更好。

说说Writeboard这个协同文字编辑软件，我们知道有很多的竞争对手的产品内建了许多复杂玄妙的功能。正因为如此，我们决定着重从“不花哨”入手。我们开发了这个程序仅仅来让人们共享和协作，而不用一些非必要的功能来使他们举步维艰。只要是不必要的我们就不做。推出后仅3个月的时间，已经超过10万个用户在使用Writeboards。

当我们开始着手Backpack这个资讯管理软件的时候，我们的敌人就是严格的组织框架和规章制度。人们应该要能够用他们自己的方式来管理他们的信息 — 而不是在一堆预先规定好的格式和众多的表格中填空。

你能从敌人那里得到的一个好处就是：一个非常清晰的营销理念。人们很容易被冲突对立挑动。并且通过把一个产品和另一个作比较能更多地了解这个产品。选中了这么一个敌人，你给人们灌输了他们想要知道的对立的信息。这样一来，他们不仅能更好更快地认识你的产品，也会站到你的这边。这是一个吸引注意力和引发产品倾向性的一个万无一失的方法。

说了这么多，必须指出的是，也不要太过着迷于竞争。过分地去分析其他产品会慢慢限制你的思维想像力。很快地看一下他们在做什么，然后就要回到你自己地理念和理想上来。

### 别老跟着领头羊

营销人员 (和几乎所有人) 都被培训要跟从领导者。自然的本能都是在思考竞争对手做对了什么，然后你在那个基础上做得更超过。 — 如果你的对手在竞价你就一定要比他更便宜，如果他在竞速你就要比他做得更快。这么一来出现的问题是万一消费者听信了别人的故事（或谎言），你再要把他说转回来就会象要说服他承认他是错的一样。没人喜欢承认他是错的。

于是你应该创造一个不同的故事来说服听众，告诉他们你的故事要比他们在其他地方听到的更重要。如果你的竞争对手是在竞速，那么你就必须转到竞价上来。如果他们强调的是健康，那么你就必须推销方便性。并不是简单地在x/y轴图表上标出“我们比较便宜”的声明，而是实实在在地用真实的完全不同于他人正在推销的故事。

—[Seth Godin](http://sethgodin./), 作家/企业家(from [做个更好的骗子](http://www./articles/article_details.asp?id=33))

### 问题的关键是什么?

老是看你的竞争对手在做什么是你给自己找麻烦的最快的方式之一。对于我们建造BlinkList这个平台来说尤其确实。从我们推出以来已经有其他10个类似的社交关系网软件相继出现。有些人已经开始在网上用并排图表来做不同软件之间功能特色的详细比较。

这是很容易误导的。我们不随大流，相反的，我们只看大方向，时时提醒自己什么是我们想要解决的问题关键，怎样去解决它。

—Michael Reining, 共同创立人, [MindValley](http://www./) 和 [Blinklist](http://www./)

* * *

## 它不该成为一种交易

## 你的激情 — 或者冷漠 — 会起作用的

如果你越不把软件当作一种交易去做，你就越能做得好。把它控制在一个你能把握的小范围内，你就有可能真正地享受过程。

如果你做这个软件一点不兴奋，那就是什么地方出了问题了。如果你只为了赶紧抢点钱做掉它，那这种影响会出现在最终产品上。同样地，如果你满怀热情地去做它，结果也会反映在产品上。人们是能够分辨出个中的区别的。

### 激情的体现

在设计这个高度主观，具争议性且难以界定的领域里，没有什么是能做到比表达激情更直接清晰的了。一个产品会使你欢欣或让你无动于衷是很显而易见的; 不管是前者或后者，要人们不发现软件背后那些创造者的感情投入是很困难的。

热情是很容易凸显出来的，漠然也是同样难以掩饰的。如果你并非带着一种诚挚的心去对待手头上的产品，那么它留下的漠然与空白是几乎不可能掩饰的，不管一个设计作品表面看来多么精细吸引人。

—Khoi Vinh, [Subtraction.com](http://www./)

### 烘培师

在美国，在这个时代生意经往往是提出一个构想，让它能盈利，在还能盈利的时候把它卖了，然后改行或不干了。这往往是一个能否挺下去的问题。对我而言: 去热爱烘培，卖你自己做的面包，人们如果喜欢它我就卖多些。我就这么把烘培事业做下去，因为我知道我在做好吃的人们爱吃的东西。

—Ian MacKaye, Fugazi的会员， Dischord Records的合伙人
(from [Salon.com People | Ian MacKaye](http://archive./people/conv/2001/01/08/mackaye/print.html))

* * *

## 保持精益 第三章

## 更小的质量

## 你越做到精益，改变越容易

一个物体的质量越大，改变方向需要的能量越多。物理世界的这个真理同样适用于商业世界。

当真理应用到Web技术的时候，改变必须是简易和低廉的。如果你不能如飞一样的改变，你就会败给能够做到的竞争者。这就是你需要追求更小质量的原因。

## 质量会由于以下因素增加

*   长期合同
*   多余的职员
*   固执的决策
*   关于会议的会议
*   厚重的流程
*   存货（物理的或者头脑的）
*   硬件，软件和技术的锁定
*   专有数据格式
*   未来被过去支配
*   长期的路线图
*   办公室政治

## 质量会由于以下因素减少

*   必要而及时的思考
*   多面手的团队成员
*   拥抱限制，而不是试着移除他们
*   更少的软件，更少的代码
*   更少的特征
*   小规模团队
*   简单
*   被拆分为正交的接口
*   开源产品
*   开放的文件格式
*   开放的文化，使承认错误更容易

更小的质量使你快速的改变方向。你可以随机应变和演进。你可以集中于好的主意，摈弃坏的。你可以倾听并尊重你的客户。你可以集成新技术现在而不是以后。你驾驶的是蒸汽船而不是飞机货舱。为这个事实骄傲吧。

举例来说，想象一个精益，小质量的公司，用更少的软件，更少的特征制造了一个产品。另一方面是一个更大质量的公司拥有一个带有明显更多软件和特征的产品。那么试想一下，随着新技术，比如Ajax，或者新概念，比如标签的出现，谁会更快的调整他的产品？拥有更多软件更多特征还带着12个月路线图的团队，还是另一个团队，拥有更少软件更少特征和一个更有机的流程——“让我们集中于我们当下应该集中精力的地方吧”。

很明显，更小质量的公司在适应市场真实需要的方面处于更有利的位置。当小质量公司已经完成转换很久以后，更大质量的公司有可能仍然在争论变化或者将他们推入官僚主义的流程中。小质量公司会领先两步于仍然在争论如何走的大质量公司。

反应快，敏捷，小质量的公司可以快速的改变他们整个业务模型，产品，特征集和营销信息。他们可以犯错并快速的修复。他们可以改变他们的优先级，产品组合和重点。还有最重要的，**他们可以改变他们的想法** 。

* * *

## 减少改变的成本

## 通过减少改变的阻碍保持灵活

改变是你最好的朋友，改变的代价越大，你越不可能做出改变。如果你的竞争对手可以比你更快的改变，你会处于一个很大的劣势。如果改变变得过于昂贵，你已经死了。

这就是保持精益真正帮助你的地方。很短时间内改变的能力是小团队与生俱来而大团队永远不会有的。这是大家伙嫉妒小家伙的地方。让巨大组织里的大团队花费数周才能改变的，对于小团队可能只需要1天。这个优势是无价的。低廉和迅速的改变是小团队的秘密武器。

请记住：所有的现金，所有的营销策略，所有的世界上的优秀人物，都买不到小带来的敏捷。

### 顺其自然

顺其自然是敏捷的根本原则之一，也是最接近纯正魔术的一个。顺其自然的特性不是设计的或内建的，它自然的发生，就如系统其余部分的动态结果。“顺其自然”来自于17世纪中期的拉丁文，意思是“无法预见的出现”。你不可能为它做计划，或者做时间表，但是你可以为它营造一个环境，让它发生并受益于它。

顺其自然一个经典的例子是鸟群的迁徙行为。计算机模拟只需要少至三个原则（按照“不要撞到一起”），你会一下子得到非常复杂的行为来描述鸟群在天空中优雅的飞行和滑翔，重组队形绕开障碍，等等。没有一个高级行为（比如躲开障碍重组形成的相同形状）是被规则规定的；都是由系统动态衍生的。

简单的规则，就像鸟群的模拟一样，导致复杂的行为。复杂的规则，就像许多国家的税法一样，导致愚蠢的行为。

许多常见的软件开发实践带来了令人遗憾的边际效应，他们消除了顺其自然行为的发生机会。大多数优化的尝试——直率的敲下一些代码——减少了互动和关联的宽度和范围，而这些正是顺其自然的来源。在鸟群迁徙的例子中，正如一个设计良好的系统，正是互动和关联创造了有趣的行为。

我们把事物绑的越紧，留给创新的、顺其自然的解决方式的空间就越少。不管是在需求被完全理解前就锁定了需求，还是不成熟地优化代码，让终端用户使用系统前引进了复杂的导航和工作流场景，结果都是一样的，一个过分复杂、愚蠢的系统，而不是顺其自然带来的清洁和优雅的系统。

保持小，保持简单，顺其自然。

—安德鲁·亨特(Andrew Hunt)， [实效程序员网站(The Pragmatic Programmers )](http://www./)

* * *

## 三个火枪手

## 用三人小组构建1.0版本

对于产品的1.0版本，请从只有三个人开始。三是一个魔力数字，提供足够人力的同时允许你保持流畅和敏捷。从一个开发者，一个设计者，和一个清道夫（一个可以在开发和设计中随意切换的人）开始。

现在显而易见的是用很少的人建造一项应用是一个挑战。但是如果你有一个正确的团队，挑战是值得的。有才能的人不会需要无尽的资源。他们会在约束限制下的工作和利用创造力解决问题的挑战中成功。缺少人力意味着你会被迫更早的应对权衡——那是没问题的。这种情况会让你更早而不是更晚的指出你的重点。你也能够与人交流，不用经常地担心他们不了解前因后果。

如果你不能够用三个人建造第一个版本，那么你或者需要更改人数或者需要缩减初始的版本。记住，保持你的第一个版本小而紧凑是没有问题的。你会快速的发现你的想法是否快速的进展，如果是，你会拥有一个清洁的简单的基础可以继续建造。

### 梅特卡夫定律(Metcalfe's Law)和项目团队

保持团队尽可能的小。梅特卡夫定律(Metcalfe's Law)，“网路的价值，为使用者的平方”，应用到项目团队的时候得到一个推论：团队的效率和团队人数的平方成反比。我开始觉得三个人对于1.0产品发布是最优的...从减少你计划添加到团队的人数开始，接着减少更多。

—Marc Hedlund, [奥莱利媒体（O'Reilly Media）](http://www./)入住企业家(entrepreneur-in-residence)

* * *

## 拥抱约束

## 让限制带领你到创新的解决方法

总是有不充足无法满足所有需要。不充足的实践。不充足的金钱。不充足的人。

*这是一件好事情*

不要被这些约束逼得发疯，拥抱他们。让他们指导你。约束驱动创新并强迫集中精力。不要试着移除它们，使用它们带来你的优势。

当37signals构建Basecamp的时候，我们有非常多的限制。我们有：

*   一个需要运营的设计公司
*   已有的客户工作
*   一个七小时的时差（David在丹麦编程序，我们其余的人在美国）
*   一个小团队
*   没有外部的资金

我们感受到了“不充足“的忧伤。所以我们让我们的盘子保持小。那时我们只能够往上方这么多。我们选取大任务，把它们分解成我们一时间能够处理的小任务。我们一步一步的行动并在前进的过程中分清主次。

”不充足“强迫我们使用创新的方法解决。我们通过始终构建更少的软件减少改变的成本。我们给人们仅仅足够的特色让它们以自己的方式来解决自己独特的问题 — 于是我们便不再是障碍。时差和空间上的距离让我们在交流中更加有效。不是人参加会议，我们的交流几乎毫无例外通过及时通讯软件和电子邮件，它们强迫我们快速的到达重点。

约束经常是伪装的优势。忘掉风险投资，长发布周期和快速招聘。代替的是，和你目前拥有的合作。

### 与枯萎作斗争

曾经被称作“缓慢增长的优雅”可以被更恰当的叫做“特征枯萎病”，就像植物上的真菌一样，它节外生枝，模糊了产品的轮廓并吸干了它的汁液。特征枯萎病的解药是，当然是，“限死的最后期限”。这导致特征被按照实现所需时间的比例被抛弃。经常出现的情况是最有用的特征需要最长的时间。于是枯萎和最后期限的结合产生了许多我们知道和喜爱的软件，包含了充足的没有用的特征。

—Jef Raskin, 作者 (摘自 ["为什么软件是它的方式"](http://jef./unpublished/widgets_of_the_week.html#anchor1152335))

* * *

## 做你自己

## 通过亲切友善和人性化来把自己和大公司区分开来

大量的小公司犯了试着装作大公司的错误。就好像他们意识到他们的规模是一个缺点，需要隐藏。太糟糕了。小型实际上可以是一个巨大的优势，尤其是在通讯方面。

小公司享受着更少的形式主义，更少的官僚主义，和更多的自由。**小公司天生和顾客更亲近。** 那意味着他们可以以一种更加直接和人性化的方式和顾客沟通。如果你是小公司，你可以用熟悉的语言而不是晦涩的行话。你的网站和产品可以用一种人类的声音，而不是操着公司的腔调。小型意味着你可以和你的顾客在一起谈话，而不是居高临下的方式。

小公司在内部的交流生同样有优势。你可以摈弃形式主义。所有事情都不再需要繁杂的流程和多重的签字确认。参与流程的人都可以开放和诚实的发言。这个没有被束缚的思想流是保持小型的巨大优势。

### 骄傲地、无所畏惧地做到真实

虽然你可能认为，顾客可以被夸大员工数字或者你的支付能力欺骗，但是精明的，你真正希望的顾客，永远会知道真相 — 无论通过直觉还是推理。尴尬的是，我曾经参与过这样的善意谎言，所有谎言都没有带来对商业最重要的东西：和真正需要你的服务的顾客建立的有意义的、持久的和互利互惠的关系。更好的应对应该是骄傲地、无所畏惧地对公司的实际规模和宽度做到真实。

—Khoi Vinh, [Subtraction.com](http://www./)

### 不论何时

不管你在哪个行业，良好的顾客服务应该是所有客户最大的要求。我们自已对于服务是这样要求的，我们的客户又怎么会有区别？从一开始，我们就做到让客户和我们的接触容易和明晰，不管他们有多少、甚至没有问题。在我们的网站上，我们列出了一个免费号码会转接到我们的手机；在我们的名片上，每个人都列出了手机号码。我们向顾客强调，不管他们有什么问题，他们随时可以联系到我们。我们的顾客感谢我们这样的信任，没有人滥用过我们的服务。

—Edward Knittel, 销售和市场总监, [KennelSource](http://www./)

* * *

## 首要任务 chapter 4

## 什么理念才是伟大的

## 通过亲切友善和人性化来把自己和大公司区分开来

竭尽全力将你的软件定位在一个点上。你的软件代表的是什么？它到底是有关什么的？在你开始设计或写任何代码之前你必须清楚地知道你做这个产品的目的 — 它的前景。把理想放大些。为什么要有它？它和其他类似产品不同的地方在哪里？

这个理念会引导你的每个决定，指引你不偏离航线。任何时候有比较出格的举动时，问自己，“我们是不是还在坚守着自己的理念做事？”

你的理念必须是简洁的。应该一句话就能把想法传达到。以下是我们每个产品的理念：

*   **Basecamp**: 项目管理即是沟通
*   **Backpack**: 把生活中的凌乱归整
*   **Campfire**: 用及时通讯软件来开展团体交流太逊了
*   **Ta-da List**: 和及时贴便条做斗争
*   **Writeboard**: 用不着麻烦微软的WORD

举个例，我们Basecamp软件的理念是，“项目管理即是沟通”。我们强烈的感觉到对一个项目的有效沟通是引导一系列责任，参与，投资和能量的关键。它把大家统到同一个目标上来增强共识。我们清楚地知道如果Basecamp能做到这点，那么其他事情也就会一一水到渠成。

这个理念引导着我们尽可能地保持Basecamp的透明性和开发性。我们不把沟通局限在公司内部，相反我们向客户敞开。我们不考虑太多权限地问题，相反我们更鼓励各方的参与。就是这个理念使我们放弃了图表，表单，报告，状态分析和电子表格的功能，相反的我们专注在优先问题的沟通上，如果每日新信息，评论，该日备忘项目和文件的共享。把有关你理念的重大决定做在前面，将来其他小的决定就会变得容易多了。

### 白板上的哲理

有一次Andy Hunt和我编写一个借记卡的交易开关。有一个要点就是同一个交易不许向用户的借记卡二次收费。也就是说，不管操作过程中怎样出错，错误都只能发生在交易最终产生前，不能允许出现重复的交易。

因此，我们在共享信息的白板上用大字写下：要从客户角度出发，容许客户犯错误的可能。

还有其他大约半打多类似这样的信条，在我们创建一些复杂的产品，需要下有技巧性的决定时，这些信条给我们指引了方向。它们使我们的软件有强大的内部凝聚力和外部的统一性。

—Dave Thomas, [一个实用编程者](http://www./)

### 编写座右铭

组织需要指导原则。需要有一个纲要; 员工每天醒来时应该要知道他们为什么而工作。这个纲要最好言简意赅，富有激情：为什么你会在这里？是什么激励了你？我把这看做是座右铭 — 一段三或四个字的描述你存在的意义。

—[Guy Kawasaki](http://www./), 作者 (摘自 [编写座右铭](http://www./comments.php?id=11963_0_1_0_C))

* * *

## 在初期时忽略细节

## 先粗后细

我们太过痴迷于细节。

*   两个原素之间的留白空间
*   完美的首个字母大写字形
*   完美的颜色
*   完美的用词
*   代码只能四行长，不能七行
*   90%与89%之差
*   760px与750px之差
*   $39/月与$49/月之差

*成功和满意来自于细节*

然而，细节中并不只有成功。细节中你还会遇到停滞不前，意见不合，无数的会议和延迟。这些东西会掩煞你的信念，降低你成功的几率。

你可有常常一整天被困死在一个设计原素或一个程序代码上？可有不时觉得你今天的进展实在算不上什么真正进展？过早专注于细节就会导致这些结果。要做完美主义者有的是时间。但不是现在。

别在第一周就担心标题字体的大小。不需要在第二周就搞定什么是最佳的绿色的色调。更不用在第三周就要把“提交”按钮向右移动三个像素。先把该放的东西放上去。然后去用它。保证它是可用的。最后才去把它调整到完美。

细节是在你使用的过程中才会显露出来的。只有在使用中你才能看到什么需要进一步关注。在使用中你才会感到缺了些什么。常常走路绊倒脚你才会清楚地上什么坑洼是需要填补的。那些是当你被迫要留意的时候才需要的细节，不是一想到细节就去搞定它。

### 魔鬼隐藏在细节中

在选修了几堂绘画课后，我彻底摆脱了“马上投入到细节中”的态度...如果你一开始就画细节十有八九出来的画作会不怎么样。事实上，从你一开始那么做就完全错了。

你必须一开始把全局的比例分配搞对。你要从最大的一块着手，慢慢过渡到最小的。草稿必须体现模糊的主题。然后你着手润色，使整体画作具有生命力。着色先从浅，中，深三个色调下手。这么一来你的草稿就会有明暗了。接下来，在你画作的其他部分都要秉持三个色调的应用原则。如此反复直到整体成型...

永远，都要从大到小去做。

—Patrick Lafleur, Creation Objet Inc. (摘自 [Signal vs. Noise](http://www./svn/archives2/getting_real_ignore_details_early_on.php))

* * *

## 当问题成为问题的时候才去担心

## 不要把时间浪费在还未成为问题的问题

你真的的需要考虑当用户到达10万以上的时候会出现的问题吗？它可能已经是两年以后的事了。

如果你现在只需要三个程序员你真的有必要雇八个吗？

你难道真的马上需要12台高端服务器即使两台就足以让你顶一年？

## 就先掠过吧

人们总是预先花很多时间在还不知道会不会发生的问题上。靠，我们推出Basecamp的时候还不知道如何向客户收费！因为产品是月付费的，我们知道还有30天的时间来搞定付费方式。我们把预先省下的时间用在解决更紧急的问题，直到产品推出后，我们才着手付费问题。结果很顺利（它迫使我们用最简单的解决方案，没什么花哨的东西）。

别整天操心还没成型的麻烦。别过度开发一个产品。到适当的时候再添加硬件和系统软件。如果进度推迟了一两个星期，别担心，还没到世界末日。只要诚实: 解释给你的客户听，说你们正经历着成长的烦恼。他们也许不会因此无比感动，但他们起码会赞同你的坦诚。

关键是: 如果你已经掌握了你需要的信息就及时做决定。这样你就能把注意力集中到需要马上解决的问题上来。

* * *

## 去网罗对味的顾客

## 找到你产品的核心市场然后就专注进去

顾客并不总是对的。现实中你要能分辨出谁是你该针对的顾客，谁是你该放弃的。庆幸的是，互联网使得发掘有共识的顾客的过程变得无比容易。

## 如果你想讨好每个人那么你什么人也讨好不了。

当我们做Basecamp的时候，我们把市场营销集中在设计公司这块上。如此缩小市场范围，我们就更有可能吸引一些有心的顾客来成为产品的追随者。要清楚地知道你的产品是为谁推出的，集中精力去讨好这部分人。

### 我们最成功的一步棋

把Campaign Monitor（市场策略观察）这个产品严格地定位在网页设计这块市场是我们走得最好得一步棋。它使我们能很容易地分辨出什么产品特色才是真正有用，更重要地是，什么特色是该舍弃地。这样一来，我们不仅能靠瞄准一个比较小地目标市场来争取更多地客人，也因这些客人都有相近的需求使得我们的开发工作更容易些。在Campaign Monitor中有大量的功能对其他人是毫无用处的完全针对网页设计者做的。

关注在一个核心市场也便于产品的宣传。我们有了这么一个定位精准的用户群，就能知道要在他们网上经常出没的地方做广告，发布他们可能会感兴趣的文章，然后逐步建立一个产品的用户社区。

—David Greiner, 创始人, [Campaign Monitor](http://www./)

* * *

## 过后才去做规模调适

## 你还没有必要现在就做调整

*"我的应用程序能否适应万人的使用规模?"*

等那真发生了再说，明白吗？如果用户的数量大大超过你的系统负荷那么恭喜！太喜欢这种麻烦事了。但在现实世界中，超大多数的网络应用程序从来都没有到达那一步。即使你真的开始超负荷了，也不会到马上就挂了的地步。你将会有时间反应和调适。还有一点就是，只有推出产品后你才有机会采集真实的数据指标，然后你才能用它们来推断哪些领域需要改进。

举例说明，推出的第一年我们的Basecamp只是在一台服务器上运作。因为这样的一个简易设置，整个实施只花了我们一周。我们并没有一开始就搞个15台服务器的集群或是花好几个月的时间担心规模调适的问题。

我们这么做有碰到什么问题吗? 有一些。但我们也发现大多数我们害怕的问题，比如短暂的系统滞缓，对用户来说并不是什么不得了的事。只要你及时和用户沟通，诚实地面对问题，他们是会谅解的。回头看，我们真的非常高兴当时并没有为了”完美的呈现“而把产品的发布推后数月。

开始阶段，要把建造强有力的核心产品做为首要任务，不要过分执迷于需不需要服务器组和是否有能力调整规模应变。 **先把一个伟大的产品推出，然后才去担心它无比成功了以后该怎么办的问题。** 否则你可能只是把精力，时间和金钱花在一个永远不会发生的预期上。

信不信由你，最大的问题不是规模调适，而是怎样达到你不得不需要去调适的那一刻。没有第一个麻烦哪来下一个麻烦。

### 反正你怎么也得回头重新审视

事实上，每个人都会有规模调整的问题，当服务人群从零到几百万的时候，所有人都必须回过头去重新审视产品设计架构的方方面面。

—Dare Obasanjo, [微软](http://gettingreal./Scaling%20Up%20and%20Startups) (摘自 [规模调适和创业公司](http://www./weblog/PermaLink.aspx?guid=067a2eb8-85c0-4886-b35f-f32b1a46eab9))

* * *

## 软件要有自己的主张

## 你的软件应该要有倾向

一些人在论证软件应该保持中立的问题。他们说开发者限制或忽视大众诉求的软件功能是一种傲慢的表现。他们说软件应该总是能随机应变的。

我们认为那都是扯淡。伟大的软件必须要有自己的理想。伟大的软件必定是有倾向的。当人们使用软件的时候他们不只是在看功能，同时他们也在寻找一个解决方案，一种理想。决定你的理想而后追求不懈。

同时谨记，如果他们不认同你的理念的话还有无数的其他理念可供选择。没有必要总追逐你永远无法讨好的人。

一个著名的例子就是wiki的最初设计过程。Ward Cunningham和他的朋友们有意把传统上认为协作文章不可或缺的许多功能都舍弃不用。他们不把每次文章的修改归功于特定哪个人，而把所有权标识都去除了。这么一来，内容就不再自我，而成为永恒。因为他们相信重要的不是谁或什么时候写的文章。这个理念改变了一切。这个决定孕育了一个以共享己任的社区，成为Wikipedia（维基百科）日后的主旋律。

我们的软件走的是一条类似的路。我们的软件并不追求成为所有人的宠儿。我们的软件是有自己的性格的。他们找寻的是志同道合的用户伙伴。他们是在和有着同样理想的用户对话。你要么上来一起，要么下车。

* * *

## 挑选功能 第五章

## 部分，而不是残缺不全

## 构建一半产品，而非产品有一半缺陷

小心“所有东西除了厨房水池”的Web应用开发途径。投身于出现的每一个合适的点子上，你将会终结在产品的一个半傻不陧的版本上。你真正想要做的是构建一个把愚蠢一脚踢开的产品。

专注于真正必须的。好点子可以尽量坦白。**摆出产品应该成为什么样的任何点子，然后砍掉一半。**减少功能直到只剩下最必要的功能。周而复始。

对于Basecamp，我们从Message开始。我们知道它是这个应用的灵魂，所以我们暂时忽略了Milestone，Todo-list，以及其他功能。这让我们基于真实的使用情况来决定下一步怎么走，而不是凭空猜测。

从一个精简，聪明的应用开始，然后让它得到关注。就能开始在你构建的坚实基础上添砖加瓦。

* * *

## 无所谓

## 只留精髓

对于“为什么你们做这个而不做那个？”这种问题，我们青睐的回答总是“因为无所谓。” 这个陈述表达了是什么让产品变得伟大。找出紧要的，略去其他。

当我们发布Campfire时，我们从第一次尝试此产品的人中听到下面一些问题：

*“为什么只有每五分钟才有时间戳？为什么不是每一行聊天都有？* 回答：无所谓。有多少次你需要每秒或者每分钟记录谈话内容？当然不是95%的情况下，5分钟时间戳足够了，因为任何更多的细节都不重要。

*“为什么你不允许粗体，斜体或者有色字体格式在聊天中出现？”* 回答：无所谓。如果你希望强调某事，使用可信赖的caps lock键，或者在词语或者段落周围投放几个 * 字符。那些方法不需要额外的软件，技术支持，处理能量，或者学习曲线。除此之外，在简单的基于文本的聊天中重量级的格式无关紧要。

*“为什么你不显示当前时间房间里的总人数？”*回答：无所谓。每个人的名字被列出来，所以你知道谁在那儿，但是12个还是16个人有什么区别？如果它不改变你的行为，那么无所谓。

这些功能如果有就更好么？当然。但是他们是不可或缺的么？他们真的重要么？不是。这就是为什么我们把他们刨除在外。最好的设计师和最好的程序员不是技能最好的，或者手指最敏捷的，或者用Photoshop用的神乎其神的人。他们是能够决定什么不重要的人。真正的收获源自于此。

你的大部分时间浪费在无关紧要的东西上。如果你能抛弃不重要的工作和思考，你将会获得不可思议的生产力。

* * *

## 从说“不”开始

## 不轻易实现功能

构建部分而不是残缺不全的秘诀是说不

每一次你对一个功能说yes时，你正在收养一个小孩。你必须带着你的孩子通过一连串事件（例如设计，实现，测试等）。一旦这个功能出现了，你就被拖住了后腿。尽量为客户少发布一个功能，再看客户是否愤怒地离开。

## 不要成为 yes-man

不轻易实现每个功能。让每个功能证明自己，并且表明自己是生还者。这就像"Fight Club."。如果那些功能就像为了进来在走廊苦候了三天，你只考虑他们。

这就是为什么你从说不开始。每一个向我们提出的 — 或者我们自己提出的 — 新功能需求都遇到一个 NO 。我们倾听但是不采取行动。最初的回应是“不是现在”，如果一个需求或者功能不停的过来，我们知道才是时候对它进一步观察。那么，只是那么，我们才开始考虑实现这个功能。

当你不采纳他们的功能建议时，你如何回复他们的抱怨呢？首当其冲的是，你要提醒他们为什么他们喜欢这个应用。“你喜欢它因为我们说NO。你喜欢它因为它没有做其他100件无关紧要的事情。你喜欢它因为它不试图始终讨好任何人。”

### “我们不想要一千个功能”

关于iTunes音乐商店，Steve Jobs 私下为独立唱片制作人做了一个小型的演讲。我喜欢的瞬间是，当观众不停地举手说：“可以做[x]么？”，“你计划添加[y]么？”。最终Ｊｏｂｓ回答：“ 等等 — 放下你们的手。听着：我知道关于iTunes应该具有很酷的特性你有一千个主意。我们也是。但是我们不想要一千个功能。那样做很恶心。创新不是关于对每件事说yes。而是对每一件事说NO，除了至关重要的特性。”

—-Derek Sivers, president and programmer, [CD Baby](http://www./) and [HostBaby](http://www./)
(from [Say NO by default](http://www./onlamp/blog/2004/08/say_no_by_default.html))

* * *

## 隐藏的成本

## 看清功能的成本

即使一个新功能通过了对其说不的阶段，你还需要去发现它隐藏的成本。

比如，我们应该注意到功能循环（带来更多功能的功能）这种现象。我们曾经有一个需求是在Basecamp里加上一个“会议标签”。如果不仔细权衡这看上去好像很简单。但是想想“会议标签”需要的不同元素：地点、时间、房间号、人员、邮件邀请、日历的整合、支持文档等等。这还不包括修改推广活动中的截图、用户预览页、常见问题及帮助页、服务条款以及更多。在你还没搞清楚它是怎么回事之前，一个简单的想法就象滚雪球一样弄得你大伤脑筋。

**对于每一个新功能你需要……**

*   1\. 对它说不
*   2\. 强迫它证明自己的价值
*   3\. 如果得到否定的答案，就此打住。如果是yes，继续往下……
*   4\. 为界面绘制草图
*   5\. 设计界面
*   6\. 编写代码
*   7-15\. 测试，改进，测试，改进，测试，改进，测试，改进……
*   16\. 检查帮助文字是否需要修改
*   17\. 更新产品预览流程（如果有必要的话）
*   18\. 更新用于销售的拷贝（如果有必要的话）
*   19\. 更新服务条款（如果有必要的话）
*   20\. 检查是否违背之前的任何许诺
*   21\. 检查价格体系是否受影响
*   22\. 上线
*   23\. 深吸一口气

* * *

## 你能把握住它么?

## 构建你有把握的

如果你发布一个会员程序，你的系统能够处理帐户和支付问题么？可能你应该只让用户根据会员身份通过信用卡支付，而不是让他每个月撰写，签名，并且邮寄一张支票。

你能承受得起1G的免费空间么？还是仅仅因为Google这么作了？可能你应该从100M开始，或者只给付费用户提供空间。

底线：构建你能够掌握的产品和服务。许诺容易遵守难。确保你所作所为是在承担范围内 — 从组织，战略和财政上

* * *

## 人本主义

## 为一般概念构建软件，并且鼓励人们创建自己的解决方案。

不要约束人的习惯。而是令软件宽容的接纳每个人自己的解决方案。给人们足以通过自己的方式解决他们自己的问题的能力。然后解决之。

当我们构建 Ta-da List的时候我们故意忽略掉了一堆东西。不能分配某人一个to-do，不能标记时间范围，条目不能分类，等等。.

We kept the tool clean and uncluttered by letting people get creative. People figured out how to solve issues on their own. If they wanted to add a date to a to-do item they could just add (due: April 7, 2006) to the front of the item. If they wanted to add a category, they could just add [Books] to the front of the item. Ideal? No. Infinitely flexible? Yes.

If we tried to build software to specifically handle these scenarios, we'd be making it less useful for all the cases when those concerns don't apply.

Do the best job you can with the root of the problem then step aside. People will find their own solutions and conventions within your general framework.

* * *

## Forget Feature Requests

## Let your customers remind you what's important

Customers want everything under the sun. They'll avalanche you with feature requests. Just check out our product forums; The feature request category always trumps the others by a wide margin.

We'll hear about "this little extra feature" or "this can't be hard" or "wouldn't it be easy to add this" or "it should take just a few seconds to put it in" or "if you added this I'd pay twice as much" and so on.

Of course we don't fault people for making requests. We encourage it and we want to hear what they have to say. Most everything we add to our products starts out as a customer request. But, as we mentioned before, your first response should be a no. So what do you do with all these requests that pour in? Where do you store them? **How do you manage them? You don't. Just read them and then throw them away.**

Yup, read them, throw them away, and forget them. It sounds blasphemous but the ones that are important will keep bubbling up anyway. Those are the only ones you need to remember. Those are the truly essential ones. Don't worry about tracking and saving each request that comes in. Let your customers be your memory. If it's really worth remembering, they'll remind you until you can't forget.

How did we come to this conclusion? When we first launched Basecamp we tracked every major feature request on a Basecamp to-do list. When a request was repeated by someone else we'd update the list with an extra hash mark (II or III or IIII, etc). We figured that one day we'd review this list and start working from the most requested features on down.

But the truth is we never looked at it again. We already knew what needed to be done next because our customers constantly reminded us by making the same requests over and over again. There was no need for a list or lots of analysis because it was all happening in real time. You can't forget what's important when you are reminded of it every day.

And one more thing: Just because x number of people request something, doesn't mean you have to include it. Sometimes it's better to just say no and maintain your vision for the product.

* * *

## Hold the Mayo

## Ask people what they *don't* want

Most software surveys and research questions are centered around what people want in a product. "What feature do you think is missing?" "If you could add just one thing, what would it be?" "What would make this product more useful for you?"

What about the other side of the coin? Why not ask people what they don't want? "If you could remove one feature, what would it be?" "What don't you use?" "What gets in your way the most?"

More isn't the answer. Sometimes the biggest favor you can do for customers is to leave something out.

### Innovation Comes From Saying No

[Innovation] comes from saying no to 1,000 things to make sure we don't get on the wrong track or try to do too much. We're always thinking about new markets we could enter, but it's only by saying no that you can concentrate on the things that are really important.

—Steve Jobs, CEO, [Apple](http://www.apple.com/) (from [The Seed of Apple's Innovation](http://www./bwdaily/dnflash/oct2004/nf20041012_4018_db083.htm))

* * *

## 操作 第六章

## 一场把软件运作起来的比赛

## 尽快地推出一个真实的产品

一个可运作的软件是积蓄动力，整合团队，去除行不通的点子的最佳方式。你必须从第一天开始就将它摆在首要位置。

做少一些功能，跳过一些细节，如果一些捷径能加快软件进度就大胆用，这些都是OK的。当你做下去的时候，你会对下一步的方向有更准确的把握。太多的故事，建模，甚至HTML演示都是比较虚的构想。一个运作着的软件是真实的。

只有一个真实的，可操作的软件才能拉近每个人对现实的理解和认同。避免了为一些草图和段落争得面红耳赤，最终发现这些都是无谓的。同时，你也会发现有些你想像中无关痛痒的事情事实上是很重要的。

真实的产品导致真实的行动。这才是你走向真理之路。

### 尽快地运作起来

我不认为我有参加过任何软件项目，不管大的或小的，是从一段漫长的规划讨论起步，不求同步发展，而又能在进度，成本或功能上成功的。把宝贵的时间浪费在发明一些不必要的或难以实施的性能上是容易的，有趣的，仅此而已，别无益处。

这个道理适用于软件开发的所有层面，“把一个产品搞起来”是一个灵活的思想。它不仅适用于一个整体的项目，微观上也适用于小规模的组件开发。

当一个可操作的组件做成后，开发者就希望知道它是否能和应用程序配合，因此他们就会尽可能快的去用它。即使一开始组件的实施并不完全，这种初期的开发协作通常会产生一个比较规范的界面和一些物尽其用的功能。

—Matt Hamer, 开发者和产品经理, [Kinja公司](http://www./)

* * *

## 冲洗一下再来过

## 在不断反复中工作着

别期望一开始就做得好。让软件自然成长，和软件对话。让它自然蜕变而进化。做为一个在线的软件是不需要在完成后才推出的。设计一些界面，使用它们，分析它们，反复地做。

与其停止在把一切都事先做好做对的思路上，不如在经反复求证得出的分析判读中前行。同时，你可以更快的推出一个积极的产品，因为你并不是一味追求一出门就完美的产品。结论是是由真实世界里的反馈，真实的目标来引导你的注意重心。

## 反复能解脱你

如果你知道过后总是要重来一遍，你就不需追求一开始就达完美。这种明了不管如何你总是得过后重新审视一些问题的理念，能引发你先把产品想法推出去看看是否可行的激情。

### 可能你比我聪明

可能你比我聪明的多。

这是完全有可能的。事实上，是非常有可能。但是，如果你象大多数人的话，那么你就会象我一样，在对看不见摸不着的东西的想象方面有困难。

人类极度善于对环境周遭的事物作出反应。一只老虎走到房间里时我们会惊慌失措，灾难性的洪水过后我们懂得去清理。遗憾的是，我们在事先计划方面，在理解我们行为带来的后果方面，在重要事情的优先排序方面，却很糟糕。

或许你是少数人中能把所有事情都把握在你的脑子里的，但这也并不重要。

Web 2.0, 在这个时代我们预定每个人都已经开始使用网络，这就为一些聪明的开发者运用人类行为的不确定性创造机会。怎么说呢？就是在允许你的用户告诉你他们想法的同时，留有空间去做改进。

最后那句同时也解释了为什么你应该以这种方式开发在线软件，以怎样的方式去推广推出产品。

把你想做到的说清楚。确保各个环节无误。然后就推出和进行改进。没有哪个人自己一个能比大伙儿加起来更聪明。

—[Seth Godin](http://sethgodin./), 作家/企业家

* * *

## 从概念到实施

## 从灵感，到草稿，到HTML，到代码

以下是我们Get Real（求实）的过程:

## 脑力激荡

先要有个点子。这产品要给我们带来什么？以Basecamp来说, 我们是要满足自己的需要。我们想要用它来发布项目的一些更新信息。我们希望能让用户一起参与。我们知道项目都有里程碑。我们希望能有个集中归档的地方让大家能回过头去温习一些旧的东西。我们想要有个全局观，从一定的高度来鸟瞰所有项目的进度。归结起来，这些假想和一些其他设想打下了我们日后着手的基础。

这个阶段并不是有关一些实施的具体细节。这是一个大方向。软件需要为我们做什么？什么时候才能知道它有用？确切的说我们要做出个什么东西来？这是高阶的理念，不是像素阶段（细节）的推敲。在这个阶段，那些细节是没有意义的。

## 纸上草稿

草稿是迅速的，实用的和便宜的，这就恰恰是你想要开始的方式。涂些东西，画些东西，方块，圆圈，线条，什么都行。把你脑子里的想法搬到纸上。这阶段的目标是把概念转成一个界面设计的粗稿。这个阶段完全是试验性的。不存在什么答案是错误的。

## 创建HTML页面

做一个HTML版本的功能界面（或一个区间界面或流程界面，如果这么做更合适的话）。发布一个实在的东西，这样一来大家就都可以看到它出现在屏幕上的样子。

以Basecamp而言，我们先做“发布一条信息”的界面，然后是“编辑信息”的界面，然后一步步下去。

先别写任何程序代码。只把HTML和CSS的框架搞出来。有关细节实施是后面的事。

## 上代码编程

当模型框架看起来过得去又兼具一些足够必要的功能时，就是开始上代码编程的时候了。

在这整个过程中要记住保持机动弹性，要有多次反复的思想准备。应该随时有这个意识：舍弃某些已完成的步骤重新来过，如果成品看起来丑陋不堪。数次重复这个过程是很自然的。

* * *

## 远离设置首选项

## 要帮你的客户决定一些小处细节

假设你将面临一个困难抉择：在一个页面上可以发布多少条信息？你的第一反应可能是，“不如做个设置首选项，在那里人们可以选择25，50，或100条每页”。这么做可是一个方便自己之门。你必须要自己做一个决定。

## 设置首选项是一种逃避困难抉择的方式

你不是运用你的专业去决定最佳的选择，相反地把问题留给了客户。表面看起来好像是你在帮客户的忙，事实上你只是会使他们更忙（客户自己已经是够忙的了）。对客户而言，面对无穷无尽的设置选项是一个很令人头痛的问题，不是一件好事。客户不应该去烦恼细枝末节 — 当是你的责任的时候就不要让别人去担待。

设置选项也是邪恶的因为他们使软件变得冗余。更多的选项就需要更多的编程代码。而且你还要花额外的时间在测试和设计上。还有很多选项排序和显示界面等你可能从来没见过的东西。这意味着隐藏的软件瑕疵：破碎的布局，凌乱的表格，奇奇怪怪的页面排序问题等等。

## 你要拿主意

替你的客户下简单的决定。这也是我们在Basecamp上用到的诀窍。每页可发布信息数是25条。项目总览页显示最近的25条信息。信息反时序排（最新的在上面）。最新近的5个项目会显示在控制面板上。不需要任何设置选项。它本来就该这样。

是的，你有可能下了一个不太好的决断。没什么大不了。如果事情发生了，人们会抱怨，会让你知道。照样，你可以做调整。Getting Real（求真求实）说的就都是有关能够一路做灵活修改的道理。

* * *

## "搞定!"

## 决定都是暂时的，那么拿定主意就继续到下一步

搞定。现在就开始把它看成一个有魔力的词。当你到达“搞定”的阶段就表明你已完成某事。一个决定已经下了，走下一步。“搞定”也表明你已经聚集了能量。

慢着，如果你搞砸了，下了一个错误的决定怎么办？没问题。 **这并不是什么开颅手术，它是一个在线应用程序。** 我们一再强调，在开发过程中你总是需要不时回过头去调整软件的功能及想法。不管你计划得多周密总有可能一半左右的东西没做好。所以，不要做“到死都要调查分析”的傻事。那样做只会慢了进度和磨去意志。

相反地，要知道以“朝前看向前走”为重。要跟上拿主意的节拍。做一个迅速简单的决断，如果它行不通那就再回头修改。

要接受多数决断都是暂时的有时效性的现实。要接受错误必将发生的现实，同时也要认识到这并不是什么大不了的，只要你能迅速改正之。执行，积蓄能量，而后前行。

### 做一个执行者

当我听说有人对自己的点子很具保护性时觉得很可笑。（那些在告诉我一些简单的概念之前希望我签定保密协定的人。）

对我而言，如果不去执行的话点子是一无用处的。它们只是倍数。执行才是价值万金的。

理由:

*   糟糕的点子 = -1
*   脆弱的点子 = 1
*   普通的点子 = 5
*   好点子 = 10
*   伟大的点子 = 15
*   超闪亮的点子 = 20

*   没有执行 = $1
*   柔弱的执行 = $1000
*   普通的执行 = $10,000
*   好的执行 = $100,000
*   伟大的执行 = $1,000,000
*   超强的执行 = $10,000,000