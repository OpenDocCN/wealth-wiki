# 【AI 创作手册】从新手入门到高手进阶

> 来源：[`cbncl0lh58.feishu.cn/docx/UEwjdIlHuovG9ixYMRXcRgTgn0c`](https://cbncl0lh58.feishu.cn/docx/UEwjdIlHuovG9ixYMRXcRgTgn0c)

最新编排：Hank（2023.04.06）

❌严禁拿公共免费资源倒卖的行为。

⭕当你使用某些公开模型进行视频、图文创作时，请标明其作者及下载来源。抵制拿他人成果给自己引流的行为！

❤希望能够让各类 AI 绘画相关资源共享，充分发挥互联网的共享优势，只有人人都参与到共享建设中才能将社区变得更加完善与开放。

⏰特别提醒：

### 🌱新手第一步！

✔AI 绘画原理剖析（by bilibili 秋葉 aaaki）：https://www.bilibili.com/read/cv21564981

底层原理，了解 SD 的三部分组成，推荐阅读

✔功能详解视频教程（by bilibili 只剩一瓶辣椒酱）：https://www.bilibili.com/video/BV1V8411s76T/

虽然基于的 webui 版本较老，但仍然实用

✔AI 绘画模型类型总结（by bilibili 秋葉 aaaki）：https://www.bilibili.com/read/cv21362202/

让你明白各种模型的使用方法

### 🧡推荐关注

✔独立研究员-星空：https://space.bilibili.com/250989068

软件整合包和教程非常好评！

✔秋葉 aaaki：https://space.bilibili.com/12566101

启动器和辅助工具非常好评！

✔小李 xiaolxl：https://space.bilibili.com/34590220

模型分享和云端部署非常好评！

### 🕹开始操练

1、SD 整合包最新整合包（by 秋葉 aaaki 2023.4.16 版本）

https://pan.baidu.com/s/1TK7UyX5lgNjdwdfcmYCI5Q

下载至本地，解压即可使用，对应使用解说视频 https://www.bilibili.com/video/BV1iM4y1y7oA/?spm_id_from=333.1007.tianma.1-1-1.click&vd_source=5a60507cc426faeebde0d8c081fef857

SD 整合包（by 独立研究员-星空 2023.1.20 版本）

下载地址 https://pan.baidu.com/s/16v1xOw1GaFkgfumjbqE08w?pwd=9hr1

下载至本地，解压即可使用，对应使用解说视频 https://www.bilibili.com/video/BV1MM411t7XX/?spm_id_from=333.999.0.0

（以上为 Win 系统，如果是苹果系统会比较麻烦，可以参考下这篇文章 https://hypform.com/d/57）

2、启动器（by 秋葉 aaaki）

下载地址 https://pan.baidu.com/s/1UQBBc0AEBze-x7Jnw-QKEw?pwd=1sky

对应使用解说视频 https://www.bilibili.com/video/BV1ne4y1V7QU/?spm_id_from=333.999.0.0

✔SD WebUI 项目本体：https://github.com/AUTOMATIC1111/stable-diffusion-webui

AUTOMATIC1111 & contributors

✔SD WebUI 插件列表：https://github.com/AUTOMATIC1111/stable-diffusion-webui/wiki/Extensions

✔命令行参数大全：https://github.com/AUTOMATIC1111/stable-diffusion-webui/wiki/Command-Line-Arguments-and-Settings#all-command-line-arguments

👑ControlNet：

【AI 绘画 Stable Diffusion——ControlNet 插件功能详解】 https://www.bilibili.com/video/BV1vL411X7U9/?share_source=copy_web&vd_source=077777231fa5422d654cac5f33767754

下载地址：https://github.com/lllyasviel/ControlNet

大型的预训练模型： https://huggingface.co/lllyasviel/ControlNet/tree/main/models

压缩模型、第三方模型： https://huggingface.co/TencentARC/T2I-Adapter/tree/main/models

（注意：模型下载到 \extensions\sd-webui-controlnet\models）

用于生成 Openpose 骨骼的 Blender 模型：https://toyxyz.gumroad.com/l/ciojz

配合 ControlNet 的手部插件：https://github.com/jexom/sd-webui-depth-lib

### 🛠模型训练

Dreambooth 本地训练：

Dreambooth+Lora 模型训练：

https://www.bilibili.com/video/BV1ss4y1s7ta/?spm_id_from=333.337.search-card.all.click

注意：本地训练对显卡配置要求较高，推荐是 12G 显存以上。

### 🎨模型网站

抱脸(hugging face)：https://huggingface.co/models

Civital：https://civitai.com/

Stable Diffusion Models：https://cyberes.github.io/stable-diffusion-models/

sdModels（自备科学工具） ：https://rentry.org/sdmodels or https://cyberes.github.io/stable-diffusion-models/sdmodels/

Stable Diffusion Textual Inversion Embeddings：https://cyberes.github.io/stable-diffusion-textual-inversion-models/

Stable Diffusion DreamBooth Models：https://cyberes.github.io/stable-diffusion-dreambooth-library/

元素法典 AI 模型收集站：https://aiguidebook.top/index.php/model/

藏丹阁第一层（by 万物炼金公会）：https://docs.qq.com/doc/DY0lFeWZuVXRCdUJU?&u=ed10236480184b9c8eb32629e7071da5

藏丹阁第二层（by 万物炼金公会）：https://docs.qq.com/doc/DY1hydmRGTENGTXdo?&u=36b7d43f425f44e29d2cf7ad850f3bf9

### 🥇推荐模型

runwayml-stable-diffusion-v1-5（SD 官方 1.5 版本）：https://huggingface.co/runwayml/stable-diffusion-v1-5

WarriorMama777-OrangeMixs（偏 3D 质感的 2.5D，俗称橘子）：https://huggingface.co/WarriorMama777/OrangeMixs

andite-pastel-mix（色彩绚丽的 2D，俗称蜡笔）：https://huggingface.co/andite/pastel-mix

Linaqruf-anything-v3.0（经典动漫 2D，简称 A3 或 Any3）：https://huggingface.co/Linaqruf/anything-v3.0

andite-anything-v4.0（Any3 迭代，包含 4.0 与 4.5 版本）：https://huggingface.co/andite/anything-v4.0

stabilityai-sd-vae-ft-mse-original（sd 官方 vae）: https://huggingface.co/stabilityai/sd-vae-ft-mse-original

nuigurumi-basil_mix（偏唯美的 3d 模型）：https://huggingface.co/nuigurumi/basil_mix

### 📚样图与 propmt 获取

（推荐）Majinai：https://majinai.art/index.php

（推荐）词图：http://www.prompttool.com/NovelAI

Black Lily（by bilibili David 的日常）：http://heizicao.gitee.io/novelai/#/book

Danbooru 标签超市（by github wfjsw /danbooru-diffusion-prompt-builder）：https://tags.novelai.dev/

魔咒百科词典（by bilibili 波西 BrackRat）：https://aitag.top/

AI 词汇加速器（by 爱发电 AcceleratorI）：https://ai.dawnmark.cn/

NovelAI 魔导书（by bilibili 西红噬土豆）：https://thereisnospon.github.io/NovelAiTag/

鳖哲法典（by Bilibili:春野三笠）：http://tomxlysplay.com.cn/#/

Danbooru tag（自备科学工具）：https://danbooru.donmai.us/wiki_pages/tag_groups

### 🛠实用小工具

在线训练图片裁剪：https://www.birme.net/?target_width=512&target_height

秋叶汉化版（by bilibili 秋葉 aaaki）：https://crop.anzu.link/

在线解析反推 tag：http://dev.kanotype.net:8003/deepdanbooru/

在线图片参数读取/模型类型分析（by bilibili 秋葉 aaaki）：https://spell.novelai.dev/

在线去除动漫图片背景：https://huggingface.co/spaces/skytnt/anime-remove-background

咒语编辑器（精简 prompt/格式修正）：http://mantra.ovsexia.com/

图片外扩（ 图片扩展 api）：https://www.painthua.com/

图片托管（免费图片托管/图床）：https://postimages.org/

HiiiMeta 丨艺术家🐇洞

![](img/a74df54d8296de870ad15d01b44da57c.png)