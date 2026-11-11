# 提示词模板

所有模板都用英文写——生成模型对英文的空间关系、材质、光线描述响应更稳。中文留给你的分镜表。

模板里的 `{{ }}` 是你要替换的部分。

---

## 0 · 先定义风格锁（每片一次）

在写任何镜头提示词之前，先定一段**风格锁**，之后每个镜头的提示词结尾都贴上它。这是跨镜头一致性最省力的手段。

```
STYLE LOCK — 写实段落
Cinematic photoreal film still, strict 16:9 aspect ratio 1920x1080, full-bleed frame
with no black bars. Desaturated palette, warm practical light against cool grey
shadows, 35mm anamorphic, shallow depth of field, natural skin texture, fine dry
grain, no purple lighting, no neon, no text, no watermark.
```

```
STYLE LOCK — 科技 / 产品段落
Premium tech visual, strict 16:9 aspect ratio 1920x1080, full-bleed frame with no
black bars. Near-black background #0A0A0B, matte black metal and brushed titanium,
single soft key light upper left, deep clean shadows, off-white highlights, at most
one hairline of muted accent colour, no neon glow, no bloom, no rainbow, no lens
flare, generous negative space, fine film grain, no text, no watermark.
```

**风格锁里一定要有"负面清单"**：no neon / no bloom / no rainbow / no text。只写正面要求，模型会自己加戏。

---

## 1 · 首帧 / 尾帧静帧

```
{{景别}} shot, {{主体与动作的静止状态}}, {{主体所在位置与朝向}},
{{环境与背景}}, {{光源方向与性质}},
{{占画幅比例}}, {{文字层要留白的位置描述}},
{{STYLE LOCK}}
```

要点：
- **只写状态，不写过程**（不要 "moving toward"、"then she turns"）
- 位置写具体（"on the right third of the frame"），不要写 "centered" 了事
- 留白写具体（"the entire left third is empty pure black"）
- 主体占画幅写百分比（"the face occupies about 35 percent of frame width"）

---

## 2 · 图生视频（单帧驱动）

```
Single-frame image-to-video, {{时长}} seconds. {{场景一句话}}.
{{逐元素的运动描述：谁在动、怎么动、动多大幅度}}.
{{镜头运动：明确写死，如 "camera pushes in very slowly, no cuts, no handheld shake"}}.
{{STYLE LOCK}}
```

要点：
- **每个会动的元素单独写一句**，不要写"让画面动起来"
- 镜头运动必须写死；不写，模型会自己选一个（通常是推镜头推太急）
- 要"不开口"就明写 `mouth stays closed the whole time and he does not speak`
- 要"保持到最后一帧"就明写 `holds to the very last frame`

---

## 3 · 图生视频（首尾帧）

```
First-to-last frame, {{时长}} seconds. {{首帧状态的一句话}}.
{{中间发生的运动，按时间顺序，可给粗略时间点}}.
{{尾帧状态的落点}}.
{{镜头运动}}. {{STYLE LOCK}}
```

要点：
- 中间的运动描述是**给模型的路径建议**，不是精确指令；模型会插值
- 首尾帧跨度大时，中间至少写一个"途经状态"，否则中间会崩
- 明确"no cuts"，否则模型可能给你硬切

---

## 4 · 口播近景（对口型参考图）

```
Medium close-up portrait for lip-sync reference, {{主体身份与年龄}},
{{发型、服装、配饰}}, facing the camera at a slight three-quarter angle,
{{嘴部状态：mouth open mid-sentence / lips softly parted}}, teeth visible,
mouth clearly visible and well lit, {{表情：natural and relaxed}},
eyes OPEN and clearly visible, gazing toward the camera, NOT squinting.
Mouth and lower face completely unobstructed, no hands in frame.
Behind {{主体}}, {{背景，虚化处理}}.
The face occupies about 35 percent of frame width, positioned slightly {{left/right}}
of centre. {{STYLE LOCK}}
```

**必须包含的四句**（缺任何一个都会出问题）：

1. `mouth clearly visible and well lit`
2. `eyes OPEN and clearly visible, NOT squinting`
3. `Mouth and lower face completely unobstructed, no hands in frame`
4. `The face occupies about 35 percent of frame width`

---

## 5 · 保持身份的图像编辑（从母图出新机位）

**这是保证"同一个人"的唯一可靠方法。**

```
Change the framing to {{新景别}} of THE SAME {{主体}} — preserve {{his/her/its}}
identity exactly: same bone structure, same {{下颌线 / 脸型}}, same eyes, same
eyebrows, same nose, same mouth and teeth, same hairline, same hair volume,
same skin tone, same {{胡渣 / 细节}}, same age. Do not beautify, do not slim
the face, do not change the hairstyle.

WARDROBE MUST MATCH THE SOURCE: {{要保留的服装与道具，点名列出，并写明它们应出现在画面什么位置}}.
Do not remove {{服装/道具}}.

EXPRESSION: {{目标表情}}. {{如需要对口型，加上嘴部状态}}.

Background: {{背景描述}}，turned into soft bokeh. {{STYLE LOCK}}
```

要点：
- **列举得越具体越像**：不要写 "same face"，要写 "same square jaw, same eyebrows"
- **服装要"点名 + 定位"**：只说 "same clothing" 常被改掉；写 "the cream apron's shoulder straps must be visible at the bottom of the frame" 才守得住
- 一次编辑只改一件事（景别 or 表情），一次改太多会两个都不到位

---

## 6 · 环境声（本地合成）

生成模型的音轨通常带音乐，不能用。环境声本地合成，用 ffmpeg 的噪声源滤波：

```bash
# 雨 + 远处车流（室外雨景）
ffmpeg -i in.mp4 -filter_complex "\
anoisesrc=color=pink:duration=6:amplitude=0.6:seed=42[r];\
anoisesrc=color=brown:duration=6:amplitude=0.6:seed=7[b];\
[r]highpass=f=450,lowpass=f=6200,tremolo=f=0.55:d=0.18,volume=0.55[rain];\
[b]lowpass=f=190,volume=0.40[rum];\
[rain][rum]amix=inputs=2:duration=longest:normalize=0,\
pan=stereo|c0=c0|c1=c0,volume=0.28[a]" \
-map 0:v -map "[a]" -t 5.17 out.mp4
```

```bash
# 室内底噪（安静的室内）
[r]lowpass=f=320,volume=0.55[hum];\
[p]highpass=f=1800,lowpass=f=5000,volume=0.10[hiss];\
[hum][hiss]amix=inputs=2:normalize=0,pan=stereo|c0=c0|c1=c0,volume=0.30[a]
```

```bash
# 人群嘈杂（餐厅、茶楼）
[p]bandpass=f=800:w=1400,tremolo=f=0.9:d=0.35,volume=0.50[crowd];\
[b]lowpass=f=220,volume=0.40[room];\
[crowd][room]amix=inputs=2:normalize=0,pan=stereo|c0=c0|c1=c0,volume=0.26[a]
```

```bash
# 沸腾 / 蒸汽（火锅、开水）
[w]highpass=f=1400,lowpass=f=7000,tremolo=f=3.2:d=0.5,volume=0.16[boil];
```

**三层结构**（人群 40% / 室内低频 40% / 细节 15%）比单层白噪声真实得多。

混音目标：环境声平均电平 **−37 dB 左右**，人声平均 **−22 dB 左右**，两者差 13–15 dB。

---

## 7 · 一句口号

> **正面要求写"要什么"，负面清单写"不要什么"，两句都要有。**
> 只写正面，模型加戏；只写负面，模型没方向。
