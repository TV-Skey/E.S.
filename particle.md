中文:

📦 E.S. 引擎：粒子组件规范文档 (Particle Components)

本手册详细说明了 .espart 资源包中 config.json 的所有可用组件。你可以通过组合这些组件，创造出从雨雪、火焰到爆炸的任意粒子特效。

📁 基础文件结构

```text
📦 effect.espart (本质是ZIP)
 ┣ 📂 particle
 ┃ ┗ 📄 config.json
 ┗ 📂 texture
   ┗ 🖼️ image.png
```

---

1. 🧩 核心组件 (Core)

name (String)

· 作用：特效的名称。
· 用法：在引擎内的大纲和模板库中显示。
· 示例："name": "TV Man 传送黑雾"

texture (String)

· 作用：贴图的文件路径。
· 用法：必须指向压缩包内 texture 文件夹下的真实文件名。
· 示例："texture": "texture/smoke.png"

quantity (Number)

· 作用：粒子单次生成的最大数量。
· 用法：数值越大，粒子越密集。手机端建议控制在 1000 以内以保证帧率。
· 示例："quantity": 500

lifetime (Number)

· 作用：单个粒子的生命周期（存活时间），单位为秒。
· 用法：时间到达后，粒子会重置回发射原点。如果 lifetime 长且 speed 慢，会形成长拖尾。
· 示例："lifetime": 2.5

---

2. 🎨 视觉组件 (Visuals)

uv (Array)

· 作用：序列帧切分（Sprite Sheet 动画）。
· 用法：格式为 [列数, 行数]。如果是一整张图填 [1, 1]；如果是 4x4 的切图（共16帧），填 [4, 4]。引擎会按每秒 15 帧的速度自动循环播放。
· 衍生：配合长 lifetime 可以做连环爆炸或翻滚的烟雾。
· 示例："uv": [4, 4]

renderMode (String)

· 作用：粒子的渲染混合模式。
· 用法：
  · "additive" (默认值)：叠加发光。黑色完全透明，亮色叠加变亮。适合火焰、火星、魔法、光效。
  · "normal"：普通混合。支持半透明 PNG 的原本 Alpha 通道。适合烟雾、水花、树叶。
· 示例："renderMode": "normal"

colorStart (String)

· 作用：粒子刚出生时的颜色（Hex 颜色码）。
· 用法：支持 #RRGGBB 格式。
· 示例："colorStart": "#ffff00" (亮黄色)

colorEnd (String)

· 作用：粒子即将消失时的颜色。
· 用法：引擎会在生命周期内进行平滑颜色渐变插值（Lerp）。
· 衍生：如果配合 additive 模式，设置 colorEnd 为 #000000，可以让火焰在熄灭前自然淡出。
· 示例："colorEnd": "#ff0000" (暗红色)

---

3. 🌀 物理场组件 (Physics)

(所有物理参数整合在 physics 对象内部)

speed (Number)

· 作用：初始发射速度。
· 用法：决定粒子飞出去的快慢。
· 示例："speed": 10.0

spread (Number)

· 作用：扩散范围（发散角度）。
· 用法：数值越大，粒子飞出时的方向越散。1.0 为漏斗状喷发，5.0 为向四周炸开。
· 示例："spread": 3.0

gravity (Number)

· 作用：模拟重力。
· 用法：
  · 负数 (-9.8)：受重力影响向下坠落（如火花、雨滴、碎石）。
  · 正数 (2.0)：反重力向上飘（如火焰、黑烟、上升的气泡）。
· 示例："gravity": -15.0

wind (Array)

· 作用：施加外部恒定风力。
· 用法：格式 [X, Y, Z]。在飞行中不断将粒子向某个方向推。
· 衍生：可以实现横向吹动的雨、斜着飘的烟。
· 示例："wind": [2.0, 0.0, 1.0] (向右和前方吹风)

drag (Number)

· 作用：空气阻力/摩擦力。
· 用法：取值范围 0.0 ~ 1.0。数值越小，粒子减速越快。
· 衍生：给火花设置 0.9，它会在喷出后迅速在空中悬停然后坠落，形成极其真实的“拖尾感”。
· 示例："drag": 0.92

---

4. 🎇 行为组件 (Behavior)

explode (Boolean)

· 作用：发射形态切换。
· 用法：
  · false (默认)：向单一方向（向上）喷泉式发射。
  · true：呈球体 360 度向外爆裂喷发。
· 示例："explode": true

---

🎁 附：组件代码实战对照

例子 1：电路短路火花 (喷出后快速落地)

```json
{
  "name": "短路火花",
  "quantity": 300,
  "lifetime": 1.0,
  "renderMode": "additive",
  "explode": false,
  "colorStart": "#ffffff",
  "colorEnd": "#ff5500",
  "physics": {
    "speed": 15.0,
    "spread": 2.0,
    "gravity": -20.0,
    "drag": 0.95
  }
}
```

效果解析：速度极快、重力极大、阻力大。粒子喷出后会被阻力迅速减速，然后被重力狠狠地砸向地面，形成极其干脆的火花。

例子 2：爆炸冲击波 (向四周炸开，带颜色渐变)

```json
{
  "name": "爆炸",
  "quantity": 800,
  "lifetime": 1.5,
  "renderMode": "additive",
  "explode": true,
  "colorStart": "#ffffaa",
  "colorEnd": "#ff0000",
  "physics": {
    "speed": 20.0,
    "spread": 5.0,
    "gravity": 0.0,
    "drag": 0.9
  }
}
```

效果解析：球形炸开，无重力，极高初速度，颜色从明亮的黄白瞬间冷却为暗红，极具爆发力。

例子 3：毒气黑雾 (缓慢上飘、半透明)

```json
{
  "name": "毒气",
  "quantity": 150,
  "lifetime": 4.0,
  "renderMode": "normal",
  "explode": false,
  "colorStart": "#44ff44",
  "colorEnd": "#112211",
  "physics": {
    "speed": 1.0,
    "spread": 1.5,
    "gravity": 1.0,
    "wind": [0.5, 0, 0],
    "drag": 0.99
  }
}
```

效果解析：极慢的速度，正数重力（往上飘），微风向右吹，极小的阻力。粒子会在空中长时间停留、翻滚并慢慢变色。

English:

📦 E.S. Engine: Particle Components Specification

This document details all available components for the .espart resource pack (config.json). By combining these components, you can create any particle effect, from simple rain and snow to complex explosions and magic fire.

📁 Basic File Structure

```text
📦 effect.espart (Actually a ZIP file)
 ┣ 📂 particle
 ┃ ┗ 📄 config.json
 ┗ 📂 texture
   ┗ 🖼️ image.png
```

---

1. 🧩 Core Components

name (String)

· Function: The name of the particle effect.
· Usage: Displayed in the engine's outliner and template library.
· Example: "name": "TV Man Smoke Teleport"

texture (String)

· Function: The file path to the texture.
· Usage: Must point to the actual file name inside the texture folder of the ZIP.
· Example: "texture": "texture/smoke.png"

quantity (Number)

· Function: Maximum number of particles generated per cycle.
· Usage: Higher values mean denser particles. For mobile devices, keep it under 1000 to maintain framerate.
· Example: "quantity": 500

lifetime (Number)

· Function: The lifespan of a single particle in seconds.
· Usage: Once the time is up, the particle resets to the emitter's origin. A long lifetime with low speed creates a long trail.
· Example: "lifetime": 2.5

---

2. 🎨 Visual Components

uv (Array)

· Function: Sprite Sheet / Sequence frame animation.
· Usage: Format is [columns, rows]. Use [1, 1] for a single image. For a 4x4 sprite sheet (16 frames), use [4, 4]. The engine automatically plays it at 15 FPS.
· Derivative: Combine with a long lifetime for looping explosions or tumbling smoke.
· Example: "uv": [4, 4]

renderMode (String)

· Function: The blending mode of the particle.
· Usage:
  · "additive" (Default): Additive Blending. Black becomes transparent, bright colors become brighter. Best for fire, sparks, magic, and glowing effects.
  · "normal": Normal Blending. Respects the original Alpha channel of transparent PNGs. Best for smoke, water splashes, and leaves.
· Example: "renderMode": "normal"

colorStart (String)

· Function: The color of the particle when it spawns (Hex code).
· Usage: Accepts #RRGGBB format.
· Example: "colorStart": "#ffff00" (Bright yellow)

colorEnd (String)

· Function: The color of the particle just before it dies.
· Usage: The engine will smoothly interpolate (Lerp) the color over the lifetime.
· Derivative: Using additive mode, setting colorEnd to #000000 makes the fire naturally fade out.
· Example: "colorEnd": "#ff0000" (Dark red)

---

3. 🌀 Physics Components

(All physics parameters are nested inside the physics object)

speed (Number)

· Function: Initial emission velocity.
· Usage: Determines how fast the particles fly out.
· Example: "speed": 10.0

spread (Number)

· Function: The spread angle of emission.
· Usage: Higher values make particles fly in a wider cone. 1.0 for a funnel, 5.0 for a radial blast.
· Example: "spread": 3.0

gravity (Number)

· Function: Simulates gravity.
· Usage:
  · Negative (-9.8): Pulls particles down (e.g., sparks, raindrops, debris).
  · Positive (2.0): Anti-gravity, pulls upward (e.g., flames, smoke, rising bubbles).
· Example: "gravity": -15.0

wind (Array)

· Function: Applies a constant external wind force.
· Usage: Format [X, Y, Z]. Constantly pushes particles in a specific direction.
· Derivative: Creates slanted rain or drifting smoke.
· Example: "wind": [2.0, 0.0, 1.0] (Blowing right and forward)

drag (Number)

· Function: Air resistance / friction.
· Usage: Range 0.0 ~ 1.0. Lower values mean faster deceleration.
· Derivative: Setting sparks to 0.9 makes them quickly slow down and hover before falling, creating a realistic "trail" feel.
· Example: "drag": 0.92

---

4. 🎇 Behavior Components

explode (Boolean)

· Function: Emission pattern toggle.
· Usage:
  · false (Default): Fires in a single direction (fountain style).
  · true: Bursts outward in a 360-degree sphere.
· Example: "explode": true

---

🎁 Appendix: JSON Templates & Effects

Example 1: Electric Short Circuit Spark (Fast descent)

```json
{
  "name": "Short Circuit Spark",
  "quantity": 300,
  "lifetime": 1.0,
  "renderMode": "additive",
  "explode": false,
  "colorStart": "#ffffff",
  "colorEnd": "#ff5500",
  "physics": {
    "speed": 15.0,
    "spread": 2.0,
    "gravity": -20.0,
    "drag": 0.95
  }
}
```

Result: Extremely fast, strong gravity, high drag. The particle shoots out, slows down quickly, and is slammed to the ground by gravity, creating a sharp spark.

Example 2: Explosion Shockwave (Radial burst with color shift)

```json
{
  "name": "Explosion",
  "quantity": 800,
  "lifetime": 1.5,
  "renderMode": "additive",
  "explode": true,
  "colorStart": "#ffffaa",
  "colorEnd": "#ff0000",
  "physics": {
    "speed": 20.0,
    "spread": 5.0,
    "gravity": 0.0,
    "drag": 0.9
  }
}
```

Result: Spherical blast, zero gravity, extreme initial velocity. Colors shift from bright white-yellow to dark red, simulating rapid cooling.

Example 3: Toxic Gas (Slowly floats up, translucent)

```json
{
  "name": "Toxic Gas",
  "quantity": 150,
  "lifetime": 4.0,
  "renderMode": "normal",
  "explode": false,
  "colorStart": "#44ff44",
  "colorEnd": "#112211",
  "physics": {
    "speed": 1.0,
    "spread": 1.5,
    "gravity": 1.0,
    "wind": [0.5, 0, 0],
    "drag": 0.99
  }
}
```

Result: Very slow speed, positive gravity (floats up), gentle wind to the right, minimal drag. The particles linger, tumble, and slowly change color in the air.