# 示例：三段开场镜头

一个真实跑通的例子。片子是科技产品宣传片，开场 11 秒，三镜头，都是图生视频。
重点看**方向列**和**首尾帧的写法**。

---

## 镜头表

| # | 时间码 | 时长 | 标题 | 产线 | **方向** |
|---|---|---|---|---|---|
| S1 | 00:00–05:00 | 5.0s | 黑场 · 芯片通电 | 图生视频（单帧） | — |
| S2 | 05:00–09:00 | 4.0s | 主体苏醒 | 图生视频（首尾帧）+ 代码叠标板 | **朝右** |
| S3 | 09:00–11:00 | 2.0s | 穿光门 | 图生视频（首尾帧） | **朝右** |

---

## S1 · 芯片通电

**画面**：几乎是纯黑。一枚芯片悬在中央偏下，只有边缘一道轮廓光，正面全暗。
低频脉冲进来后，表面电路细线一道道亮起。最后一秒，核心透出一缕极细的竖直微光。

**镜头**：微距固定机位，全程只推进约 10%。

**锚**：
- 首帧：极暗微距，芯片居中偏下，占画幅约 1/6，只有边缘轮廓光，85% 画面纯黑
- 可选尾帧：同一芯片，电路细线亮起，核心升起一缕竖光，70% 画面纯黑

**验证点**：芯片在首尾帧里必须是同一个角度和同一处高光，否则中间会变形。

---

## S2 · 主体苏醒

**画面**：光开始在空中编织。先是半透明细线骨架，逐渐长出实体材质，最后翅膀猛地展开一次，主体悬停。

**镜头**：微距仰角，轻微手持浮动（±2px 以内），焦点从下方物体跟到主体身上。

**锚**：
- 首帧：线框阶段，主体位于画面中央，**头部朝右**，翅膀未展开，只有光线与发光节点
- 尾帧：完全成形，双翼展开，**位于画面左侧三分之一，头部朝右**，右侧三分之一留黑给文字层

**验证点**：
- 首尾帧朝向必须都是"朝右"
- 尾帧主体在左、留白在右 → 文字层贴右边，不是左边（这一点经常被写反）

---

## S3 · 穿光门

**画面**：主体加速向右冲，前方立着一道薄如刀口的竖直光缝。撞进去，光缝炸开，右半画面被柔白吃掉。
主体**穿过后继续向右，从右边缘出画**，光爆留在身后，画面左侧仍是黑的。

**镜头**：跟拍横移，速度骤增，末尾 3 帧带运动模糊与短过曝。

**锚**：
- 首帧：主体在中偏左，**朝右**；光缝立在右侧，薄如刀口
- 尾帧：光爆在右中，主体已到**右边缘**继续向右；左侧三分之一仍是黑场

**验证点**：
- 主体"左进右出"，与首帧方向一致
- 尾帧里光爆的位置 = 首帧里光缝的位置（都在右侧）

---

## 这里曾经犯过的错（值得记住）

**第一版 S3 尾帧被写成了"主体从左侧飞出"。**

问题在于：首帧里主体从左边出发、往右飞、撞的是右边的光缝——那么它穿过之后应该继续往右出画，不可能回到左边。

写锚的时候只写了"炸开"和"剪影"，没写"往哪边出去"，结果生成出来的尾帧方向是反的。剪起来会像主体凭空回到起点。

**修正**：把尾帧重写为"已穿过，位于右边缘继续向右，光爆在身后"。改完方向就自洽了。

**教训**：转场镜头的锚，一定要在结尾写清"动作结束后主体往哪个方向"。这是最容易漏、最难在单帧里看出来的错误。

---

## 风格锁（三段共用）

```
Premium tech visual, strict 16:9 aspect ratio 1920x1080, full-bleed frame with no
black bars. Near-black background #0A0A0B, matte black metal and brushed titanium,
frosted glass, single soft key light upper left, deep clean shadows, off-white
highlights, at most one hairline of muted accent colour, no neon glow, no bloom,
no rainbow, no lens flare, generous negative space, fine film grain, no text,
no watermark.
```

---

## 三段的提示词骨架

**S1（单帧驱动）**

```
Single-frame image-to-video, 5 seconds. Near-total darkness with a matte black
{{物体}} whose machined edge catches a faint rim of pale grey light. Over five
seconds the etched hairlines on its surface illuminate one by one like breathing,
and in the final second a single thin column of pale light begins to rise from the
centre. Extremely slow 10% push-in, locked-off macro camera, 85mm macro lens, very
shallow depth of field, no colour cast, no bloom halo, no lens flare, fine grain.
```

**S2（首尾帧）**

```
First-to-last frame, 4 seconds. A thin column of pale light above the {{物体}}
condenses and weaves itself into a translucent wireframe silhouette; the wireframe
resolves into matte titanium plates and frosted glass, then the wings snap open and
the subject holds a steady hover on the LEFT third facing RIGHT, leaving the right
third empty black. Locked camera with ±2px handheld float only, focus racks upward,
single soft key light upper left, no coloured glow, no bloom, no lens flare.
```

**S3（首尾帧）**

```
First-to-last frame, 2 seconds. The subject accelerates RIGHTWARD toward a single
infinitely thin vertical slit of pale light standing in the black void; it strikes
the slit and the slit flares open, washing the RIGHT half of frame with soft white
light, while the subject continues RIGHTWARD and exits through the RIGHT edge of
frame, leaving the flare behind it. Fast lateral tracking with sudden acceleration,
motion blur and brief overexposure on the final three frames, no rainbow prism,
no coloured glow.
```

注意 S3 里 `continues RIGHTWARD and exits through the RIGHT edge` 这句——就是它把方向钉住的。

---

## 验收记录

| 检查项 | 结果 |
|---|---|
| 三段朝向一致（朝右） | ✅（曾因 S2 尾帧朝左而翻转一次，已镜像修正） |
| 首尾帧同材质同光源 | ✅ |
| 文字层留白充足 | ✅（尾帧主体在左，右侧留黑） |
| 时长与分镜表一致 | ✅ 5.0 + 4.0 + 2.0 = 11.0s |
| 无烧入文字 | ✅ |
