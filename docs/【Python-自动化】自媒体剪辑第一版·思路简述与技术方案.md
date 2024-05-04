<!--yml
category: 视频
date: 2023-09-17 14:36:48
-->

# 【Python 自动化】自媒体剪辑第一版·思路简述与技术方案

> 来源：[https://blog.csdn.net/wizardforcel/article/details/132633370](https://blog.csdn.net/wizardforcel/article/details/132633370)

大家都知道我主业是个运维开发（或者算法工程师），每天时间不多，但我又想做自媒体。然后呢，我就想了个方案，每天起来之后写个短视频的脚本，包含一系列图片和文字，然后上班的时候给它提交到流水线上跑，下班之前就能拿到视频，然后往各大平台上一传，是不是挺美滋滋的。

我和我之前的合伙人一说，他就觉得做短视频没啥用，不过还是按我说的做，出了一个脚本，我一看什么玩意儿，根本就没办法跑起来。无奈之下，我重新写了现在我要展示的这个版本。后来这个合伙人就装逼失败跑路了，大快人心。这个版本不长，也就3~400行，我就在想，连一个几百行的程序都写不好，还谈啥副业，真是可笑。

首先程序接受这样一个 YAML 剧本，定义了图片和文字内容。这里我把MR数据杨第一版引擎所使用的素材做成了剧本：

```
name: 人气直播主饶方晴气质笑颜无害又迷人
format: mp4
imgSize: [1280, 768]
contents:
  - type: image:file
    value: asset/1.jpg
  - type: audio:tts
    value: 人气直播主饶方晴，气质笑颜无害又迷人。
  - type: image:file
    value: asset/2.jpg
  - type: audio:tts
    value: 这笑容是要融化多少单身汉！小编今天要为大家送上这位白嫩系的正妹「亮亮Sunny」。
  - type: image:file
    value: asset/3.jpg
  - type: audio:tts
    value: 浑身白皙的肌肤吹弹可破一般稚嫩，留着柔顺长发的她衬着那张清新又甜美的脸蛋。
  - type: image:file
    value: asset/4.jpg
  - type: audio:tts
    value: 水汪大眼搭上迷人的微笑，让人对到一眼就深深着迷。
  - type: image:file
    value: asset/5.jpg
  - type: audio:tts
    value: 往下一望还有一双壮美的峰景，深厚的事业线简直不留余地给司机们啊！
  - type: image:file
    value: asset/6.jpg
  - type: audio:tts
    value: 亮亮本名又叫作「饶方晴」，身为美女主播的她凭着那亮丽的外表和姣好的体态。
  - type: image:file
    value: asset/7.jpg
  - type: audio:tts
    value: 总能无时无刻圈下不少忠实粉丝，开朗又活泼的个性更是深受大家喜爱。
  - type: image:file
    value: asset/8.jpg
  - type: audio:tts
    value: 大方的她平日也总在IG上放晒迷人又性感的自拍照。
  - type: image:file
    value: asset/9.jpg
  - type: audio:tts
    value: 不只是濠乳沟乍现，连笔直又纤长的美腿也让腿控们大饱眼福呢！ 
```

很简单是吧，就是一个图片一个文本这样。真的不用太多，精雕细琢没意义，樊登说了，自媒体平台靠走量刷流量池取胜。我在此奉劝大家彻底抛弃完美主义。

## 子命令回调

因为我这个功能是一个工具集的子命令，回调就是子命令的入口。我们看看回调咋写的：

```
def autovideo(args):
    cfg_fname = args.config
    if not cfg_fname.endswith('.yml'):
        print('请提供 YAML 文件')
        return
    cfg_dir = path.dirname(cfg_fname)
    user_cfg = yaml.safe_load(open(cfg_fname, encoding='utf8').read())
    update_config(user_cfg, cfg_dir)

    # 素材预处理
    preproc_asset(config)
    # 转换成帧的形式
    frames = contents2frame(config['contents'])
    # 组装视频
    video = make_video(frames)
    if config['format'] != 'mp4':
        video = ffmpeg_conv_fmt(video, 'mp4',  config['format'])
    # 写文件
    video_fname = fname_escape(config['name']) + '.' + config['format']
    print(video_fname)
    open(video_fname, 'wb').write(video) 
```

一共这么五步：（1）读取配置（2）预处理（3）模块划分（4）组装（5）写文件，大功告成。

## 读取配置

（1）将用户传入的配置覆盖程序默认配置，（2）将所有素材对于剧本的相对路径转换成对于 CWD 的相对路径，（3）加载外部模块，覆盖 TTI 和 TTS 函数。

```
def update_config(user_cfg, cfg_dir):
    global tts
    global tti

    config.update(user_cfg)
    if not config['contents']:
        raise AttributeError('内容为空，无法生成')

    for cont in config['contents']:
        if cont['type'].endswith(':file'):
            cont['value'] = path.join(cfg_dir, cont['value'])
    if config['header']:
        config['header'] = path.join(cfg_dir, config['header'])
    if config['footer']:
        config['footer'] = path.join(cfg_dir, config['footer'])

    if config['external']:
        mod_fname = path.join(cfg_dir, config['external'])
        exmod = load_module(mod_fname)
        if hasattr(exmod, 'tts'): tts = exmod.tts
        if hasattr(exmod, 'tti'): tti = exmod.tti 
```

## 预处理

（1）遍历每个内容，分别处理不同类型：

*   `:file`是文件，直接读取
*   `image:dir`需要从资源目录随机挑选一张指定关键词的图片
*   `:url`是网络资源，直接下载
*   `audio:tts`是机器朗读的音频，直接调用 TTS 函数
*   `image:color`是纯色图片，用 OpenCV 生成
*   `image:tti`是文成图，直接调用 TTI 函数
*   `audio:blank`是空白音频，直接用 SciPy 生成

（2）剪裁图片到配置规定的尺寸，（3）如果第一张不是图片，将第一个图片提前（当前这块逻辑也可以改成如果第一张不是图片就插入一个纯黑的图片）。

```
def preproc_asset(config):
    # 加载或生成内容
    for cont in config['contents']:
        if cont['type'].endswith(':file'):
            cont['asset'] = open(cont['value'], 'rb').read()
        elif cont['type'] == 'image:dir':
            assert config['assetDir']
            cont['asset'] = get_rand_asset_kw(config['assetDir'], cont['value'], is_pic)
        elif cont['type'].endswith(':url'):
            url = cont['value']
            print(f'下载：{url}')
            cont['asset'] = request_retry('GET', url).content
        elif cont['type'] == 'audio:tts':
            text = cont['value']
            print(f'TTS：{text}')
            cont['asset'] = tts(text)
        elif cont['type'] == 'image:color':
            bgr = cont['value']
            if isinstance(bgr, str):
                assert re.search(r'^#[0-9a-fA-F]{6}$', bgr)
                r, g, b = int(bgr[1:3], 16), int(bgr[3:5], 16), int(bgr[5:7], 16)
                bgr = [b, g, r]
            cont['asset'] = gen_mono_color(config['size'][0], config['size'][1], bgr)
        elif cont['type'] == 'image:tti':
            text = cont['value']
            print(f'TTI：{text}')
            cont['asset'] = tti(text)
        elif cont['type'] == 'audio:blank':
            cont['asset'] = gen_blank_audio(cont['value'])

    config['contents'] = [
        c for c in config['contents']
        if 'asset' in c
    ]

    # 剪裁图片
    w, h = config['size']
    mode = config['resizeMode']
    for c in config['contents']:
        if c['type'].startswith('image:'):
            c['asset'] = resize_img(c['asset'], w, h, mode)

    # 如果第一张不是图片，则提升第一个图片
    idx = -1
    for i, c in enumerate(config['contents']):
        if c['type'].startswith('image:'):
            idx = i
            break
    if idx == -1:
        print('内容中无图片，无法生成视频')
        sys.exit()
    if idx != 0:
        c = config['contents'][idx]
        del config['contents'][idx]
        config['contents'].insert(0, c) 
```

## 模块划分

每个模块相对独立，每个模块只能有一个图片，但可以有多个音频。所以将单个图片和其后的连续音频划分到一个模块中。每个模块单独组装，之后再连接起来，这样比较方便处理字幕。

```
def contents2frame(contents):
    frames = []
    for c in contents:
        if c['type'].startswith('image:'):
            frames.append({
                'image': c['asset'],
                'audios': [],
            })
        elif c['type'].startswith('video:'):
            frames.append({
                'video_noaud': c['asset'],
                'audios': [],
            })
        elif c['type'].startswith('audio:'):
            if len(frames) == 0: continue
            frames[-1]['audios'].append({
                'audio': c['asset'],
                'len': audio_len(c['asset']),
                'subtitle': c['value'] if c['type'] == 'audio:tts' else '',
            })
    for f in frames:
        f['len'] = sum([a['len'] for a in f['audios']])
        f['video_noaud'] = img_nsec_2video(f['image'], f['len'], config['fps'])
        f['audio'] = (
            f['audios'][0]['audio'] 
            if len(f['audios']) == 1 
            else ffmpeg_cat([a['audio'] for a in f['audios']], 'mp3')
        )
        f['video'] = ffmpeg_merge_video_audio(f['video_noaud'], f['audio'], audio_fmt='mp3')
        f['srt'] = gen_srt(f['audios'])
        f['video'] = ffmpeg_add_srt(f['video'], f['srt'])
    return frames 
```

这里的逻辑是，（1）划分模块，将每个图片连同后面的连续音频划分进一个模块。

（2）对于每个模块，根据音频求出整个模块长度，制作无声视频，连接音频，制作字幕，然后把这些都连接起来。

## 字幕处理

模块划分这一步需要为每个模块生成 SRT 文件。

```
 def srt_time_fmt(num):
    sec = int(num) % 60
    min_ = int(num) // 60 % 60
    hr = int(num) // 3600
    msec = int(num * 1000) % 1000
    return f'{hr:02d}:{min_:02d}:{sec:02d},{msec:03d}'

# 生成字幕
def gen_srt(audios):
    # 提取 audios 数组中的字幕
    subs = [
        {
            'text': a['subtitle'],
            'len': a['len'],
        }
        for a in audios
    ]
    # 将每个字幕按指定长度分割
    for s in subs:
        text = s['text']
        if not text: continue
        parts = split_text_even(text, config['subtitleMaxLen'])
        s['parts'] = [
            {
                'text': p,
                'len': len(p) / len(text) * s['len'],
            }
            for p in parts
        ]
    # 将分割后的字幕替换原字幕
    subs = sum([
        s.get('parts', s) for s in subs
    ], [])
    # 计算起始时间
    offset = 0
    for s in subs:
        s['start'] = offset
        offset += s['len']
    # 组装 SRT 文件
    srts = []
    for i, s in enumerate(subs):
        if not s['text']: continue
        st, ed = srt_time_fmt(s['start']), srt_time_fmt(s['start'] + s['len'])
        text = s['text']
        srts.append(f'{i+1}\n{st} --> {ed}\n{text}\n')
    srt = '\n'.join(srts)
    return srt 
```

这里我们把每个音频挑出来，从里面获取字幕和长度。然后对于每个字幕，将其按照指定好的长度分割，按照每个片段的比例计算其长度。最后给每条字幕计算起始时间，并组装 SRT 文件。

为了防止字母分割之后有个小尾巴，我们根据最大长度计算片段数量，然后按照这个数量平均分割。

```
def split_text_even(text, maxlen):
    textlen = len(text)
    num = math.ceil(textlen / maxlen)
    reallen = textlen // num
    res = [text[i:i+reallen] for i in range(0, textlen, reallen)]
    if textlen % num != 0:
        res[-1] += text[:-textlen%num]
    return res 
```

## 组装视频

这就简单了，合并所有模块的视频，并添加片头片尾（如果存在的话）。

```
def make_video(frames):
    # 合并视频
    video = ffmpeg_cat([f['video'] for f in frames])
    # 合并片头片尾
    if config['header']:
        header = open(config['header'], 'rb').read()
        video = ffmpeg_cat([header, video])
    if config['footer']:
        footer = open(config['footer'], 'rb').read()
        video = ffmpeg_cat([video, footer])
    return video 
```

## 工具函数

工具函数基本都用 FFMPEG 或者 OpenCV 实现的。

### `tts`

直接调用 EdgeTTS：

```
def edgetts_cli(text, voice='zh-CN-XiaoyiNeural', fmt='mp3'):
    fname = path.join(tempfile.gettempdir(), uuid.uuid4().hex + '.' + fmt)
    cmd = [
        'edge-tts', '-t', text, '-v', voice, '--write-media', fname,
    ]
    print(f'cmd: {cmd}')
    subp.Popen(cmd, shell=True).communicate()
    res = open(fname, 'rb').read()
    safe_remove(fname)
    return res 
```

然后外面包了一层加了个缓存：

```
def tts(text):
    hash_ = hashlib.md5(text.encode('utf8')).hexdigest()
    cache = load_tts(hash_, 'none')
    if cache: return cache
    data = edgetts_cli(text)
    save_tts(hash_, 'none', data)
    return data

def load_tts(hash_, voice):
    fname = path.join(DATA_DIR, f'{hash_}-{voice}')
    if path.isfile(fname):
        return open(fname, 'rb').read()
    else:
        return None

def save_tts(hash_, voice, data):
    safe_mkdir(DATA_DIR)
    fname = path.join(DATA_DIR, f'{hash_}-{voice}')
    open(fname, 'wb').write(data) 
```

### `tti`

这个函数没有实现，可以在外部脚本里面实现。

### `get_rand_asset_kw`

用`os.walk`遍历指定目录及其子目录中的文件，使用过滤函数和关键词过滤，再随机挑选。

```
def get_rand_asset_kw(dir, kw, func_filter=is_pic):
    tree = list(os.walk(dir))
    fnames = [path.join(d, n) for d, _, fnames in tree for n in fnames]
    pics = [n for n in fnames if func_filter(n)]
    cand = [n for n in pics if kw in n]
    return random.choice(cand) if len(cand) else  random.choice(pics) 
```

### `gen_mono_color`

用 NumPy 生成`[H, W, 3]`尺寸的 BGR 图片，然后每个第二维都赋值为指定 BGR。最后拿 OpenCV 编码。

```
def gen_mono_color(w, h, bgr):
    assert len(bgr) == 3
    img = np.zeros([h, w, 3])
    img[:, :] = bgr
    img = cv2.imencode('.png', img, [cv2.IMWRITE_PNG_COMPRESSION, 9])[1]
    return bytes(img) 
```

### `gen_blank_audio`

用 NumPy 生成`[SR * L]`尺寸的纯零数组，用`sp.io`写到内存流里面。

```
def gen_blank_audio(nsec, sr=22050, fmt='wav'):
    audio = np.zeros(int(nsec * sr), dtype=np.uint8)
    bio = BytesIO()
    wavfile.write(bio, sr, audio)
    audio = bio.getvalue()
    if fmt != 'wav':
        audio = ffmpeg_conv_fmt(audio, 'wav', fmt)
    return audio 
```

### `ffmpeg_get_info`

调用 FFMPEG 获取视频的时长、FPS、SR。

```
def ffmpeg_get_info(video, fmt='mp4'):
    if isinstance(video, bytes):
        fname = path.join(tempfile.gettempdir(), uuid.uuid4().hex + '.' + fmt)
        open(fname, 'wb').write(video)
    else:
        fname = video
    cmd = ['ffmpeg', '-i', fname]
    print(f'cmd: {cmd}')
    r = subp.Popen(
            cmd, stdout=subp.PIPE, stderr=subp.PIPE, shell=True
    ).communicate()
    text = r[1].decode('utf8')
    res = {}
    m = re.search(r'Duration:\x20(\d+):(\d+):(\d+)(.\d+)', text)
    if m:
        hr = int(m.group(1))
        min_ = int(m.group(2))
        sec = int(m.group(3))
        ms = float(m.group(4))
        res['duration'] = hr * 3600 + min_ * 60 + sec + ms
    m = re.search(r'(\d+)\x20fps', text)
    if m:
        res['fps'] = int(m.group(1))
    m = re.search(r'(\d+)\x20Hz', text)
    if m:
        res['sr'] = int(m.group(1))
    if isinstance(video, bytes):
        safe_remove(fname) 
```

### `resize_img`

保持长宽比缩放图片。有两种模式：`wrap`将图片缩放到不大于指定尺寸的最大尺寸，然后填充不足的部分；`fill`缩放到大于指定尺寸的最小尺寸，然后切掉多余的部分。

```
# 缩放到最小填充尺寸并剪裁
def resize_img_fill(img, nw, nh):
    fmt_bytes = isinstance(img, bytes)
    if fmt_bytes:
        img = cv2.imdecode(np.frombuffer(img, np.uint8), cv2.IMREAD_COLOR)
    h, w, *_ = img.shape
    # 计算宽高的缩放比例，使用较大值等比例缩放
    x_scale = nw / w
    y_scale = nh / h
    scale = max(x_scale, y_scale)
    rh, rw = int(h * scale), int(w * scale)
    img = cv2.resize(img, (rw, rh), interpolation=cv2.INTER_CUBIC)
    # 剪裁成预定大小
    cut_w = rw - nw
    cut_h = rh - nh
    img = img[
        cut_h // 2 : cut_h // 2 + nh,
        cut_w // 2 : cut_w // 2 + nw,
    ]
    if fmt_bytes:
        img = bytes(cv2.imencode('.png', img, IMWRITE_PNG_FLAG)[1])
    return img

# 缩放到最大包围并填充
def resize_img_wrap(img, nw, nh):
    fmt_bytes = isinstance(img, bytes)
    if fmt_bytes:
        img = cv2.imdecode(np.frombuffer(img, np.uint8), cv2.IMREAD_COLOR)
    h, w, *_ = img.shape
    # 计算宽高的缩放比例，使用较小值等比例缩放
    x_scale = nw / w
    y_scale = nh / h
    scale = min(x_scale, y_scale)
    rh, rw = int(h * scale), int(w * scale)
    img = cv2.resize(img, (rw, rh), interpolation=cv2.INTER_CUBIC)
    # 填充到预定大小
    pad_w = nw - rw
    pad_h = nh - rh
    img = cv2.copyMakeBorder(
        img, pad_h // 2, pad_h - pad_h // 2, pad_w // 2, pad_w - pad_w // 2, 
        cv2.BORDER_CONSTANT, None, (0,0,0)
    ) 
    if fmt_bytes:
        img = bytes(cv2.imencode('.png', img, IMWRITE_PNG_FLAG)[1])
    return img

def resize_img(img, nw, nh, mode='wrap'):
    assert mode in ['wrap', 'fill']
    func_resize_img = resize_img_wrap if mode == 'wrap' else resize_img_fill
    return func_resize_img(img, nw, nh) 
```

### `imgs_nsecs_2video`

将指定图片做成长度固定的视频。就是使用秒数乘以 FPS，作为帧数，将帧塞进`VideoWriter`做成视频即可。

```
def imgs2video(imgs, fps=30):
    ofname = path.join(tempfile.gettempdir(), uuid.uuid4().hex + '.mp4')
    fmt = cv2.VideoWriter_fourcc('M', 'P', '4', 'V')
    w, h = get_img_size(imgs[0])
    vid = cv2.VideoWriter(ofname, fmt, fps, [w, h])
    for img in imgs:
        if isinstance(img, bytes):
            img = cv2.imdecode(np.frombuffer(img, np.uint8), cv2.IMREAD_COLOR)
        vid.write(img)
    vid.release()
    res = open(ofname, 'rb').read()
    safe_remove(ofname)
    return res

def get_img_size(img):
    if isinstance(img, bytes):
        img = cv2.imdecode(np.frombuffer(img, np.uint8), cv2.IMREAD_COLOR)
    assert isinstance(img, np.ndarray) and img.ndim in [2, 3]
    return img.shape[1], img.shape[0]

def img_nsec_2video(img, nsec, fps=30):
    count = math.ceil(fps * nsec)
    imgs = [img] * count
    return imgs2video(imgs, fps) 
```

### `ffmpeg_*`

这些都是 FFMPEG 命令行的包装，注意处理好编码和反斜杠什么的就可以。

```
def ffmpeg_conv_fmt(video, from_, to):
    prefix = uuid.uuid4().hex
    from_fname = path.join(tempfile.gettempdir(), f'{prefix}.{from_}')
    to_fname = path.join(tempfile.gettempdir(), f'{prefix}.{to}')
    open(from_fname, 'wb').write(video)
    cmd = ['ffmpeg', '-i', from_fname, '-c', 'copy', to_fname, '-y']
    print(f'cmd: {cmd}')
    subp.Popen(cmd, shell=True).communicate()
    res = open(to_fname, 'rb').read()
    safe_remove(from_fname)
    safe_remove(to_fname)
    return res

def ffmpeg_cat(videos, fmt='mp4'):
    tmpdir = path.join(tempfile.gettempdir(), uuid.uuid4().hex)
    safe_mkdir(tmpdir)
    for i, video in enumerate(videos):
        fname = path.join(tmpdir, f'{i}.{fmt}')
        open(fname, 'wb').write(video)
    video_fnames = [
        'file ' + path.join(tmpdir, f'{i}.{fmt}').replace('\\', '\\\\')
        for i in range(len(videos))
    ]
    video_li_fname = path.join(tmpdir, f'list.txt') 
    open(video_li_fname, 'w', encoding='utf8').write('\n'.join(video_fnames))
    ofname = path.join(tmpdir, f'res.{fmt}')
    cmd = [
        'ffmpeg', '-f', 'concat', '-safe', '0',
        '-i', video_li_fname, '-c', 'copy', ofname, '-y',
    ]
    print(f'cmd: {cmd}')
    subp.Popen(cmd, shell=True).communicate()
    res = open(ofname, 'rb').read()
    safe_rmdir(tmpdir)
    return res

def ffmpeg_add_srt(video, srt, video_fmt='mp4'):
    tmpdir = path.join(tempfile.gettempdir(), uuid.uuid4().hex)
    safe_mkdir(tmpdir)
    vfname = path.join(tmpdir, f'video.{video_fmt}')
    open(vfname, 'wb').write(video)
    sfname = path.join(tmpdir, f'subtitle.srt')
    open(sfname, 'w', encoding='utf8').write(srt)
    res_fname = path.join(tmpdir, f'merged.{video_fmt}')
    cmd = ['ffmpeg', '-i', f'video.{video_fmt}', '-vf', f'subtitles=subtitle.srt', res_fname, '-y']
    '''
    cmd = [
        'ffmpeg', '-i', vfname, '-i', sfname, 
        '-c', 'copy', res_fname, '-y',
    ]
    if video_fmt == 'mp4': cmd += ['-c:s', 'mov_text']
    '''
    print(f'cmd: {cmd}')
    subp.Popen(cmd, shell=True, cwd=tmpdir).communicate()
    res = open(res_fname, 'rb').read()
    safe_rmdir(tmpdir)
    return res

def ffmpeg_merge_video_audio(video, audio, video_fmt='mp4', audio_fmt='mp4'):
    tmpdir = path.join(tempfile.gettempdir(), uuid.uuid4().hex)
    safe_mkdir(tmpdir)
    vfname = path.join(tmpdir, f'video.{video_fmt}')
    v0fname = path.join(tmpdir, f'video0.{video_fmt}')
    open(vfname, 'wb').write(video)
    afname = path.join(tmpdir, f'audio.{audio_fmt}')
    a0fname = path.join(tmpdir, f'audio0.{audio_fmt}')
    open(afname, 'wb').write(audio)
    res_fname = path.join(tmpdir, f'merged.{video_fmt}')
    cmds = [
        ['ffmpeg', '-i', vfname, '-vcodec', 'copy', '-an', v0fname, '-y'],
        ['ffmpeg', '-i', afname, '-acodec', 'copy', '-vn', a0fname, '-y'],
        ['ffmpeg', '-i', a0fname, '-i', v0fname, '-c', 'copy', res_fname, '-y'],
    ]
    for cmd in cmds:
        print(f'cmd: {cmd}')
        subp.Popen(cmd, shell=True).communicate()
    res = open(res_fname, 'rb').read()
    safe_rmdir(tmpdir)
    return res 
```

### `load_module`

用一些骚操作加载外部模块。（1）创建加载目录，并添加到`sys.path`。（2）为模块起个名字，并将文件内容用这个名字保存到加载目录中。（3）导入模块，删除文件。

```
def load_module(fname):
    if not path.isfile(fname) or \
        not fname.endswith('.py'):
        raise FileNotFoundError('外部模块应是 *.py 文件')
    tmpdir = path.join(tempfile.gettempdir(), 'load_module')
    safe_mkdir(tmpdir)
    if tmpdir not in sys.path:
        sys.path.insert(0, tmpdir)
    mod_name = 'x' + uuid.uuid4().hex
    nfname = path.join(tmpdir, mod_name + '.py')
    shutil.copy(fname, nfname)
    mod = __import__(mod_name)
    safe_remove(nfname)
    return mod 
```

## 未来规划

*   添加视频整合能力
*   添加 PPT（富文本转图片）整合能力
*   没了。。。