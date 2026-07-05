# 🎂 生日惊喜页面 — 项目文档

> **线上地址**：https://robinspin77.github.io/birthday-surprise/
> **GitHub 仓库**：https://github.com/RobinSpin77/birthday-surprise
> **部署平台**：GitHub Pages（main 分支自动部署）

---

## 一、项目概述

一个 H5 生日惊喜页面，女朋友点开链接后能看到 5 个按顺序排列的活动卡片：

1. 🛒 购物车心愿单
2. 🍰 甜品上海大作战
3. 🧗 人生新技能解锁（攀岩）
4. 🥘 共进西班牙晚餐
5. 🎵 解锁音乐会体验

**核心机制**：
- 🔒 **锁定状态**：标题模糊、显示倒计时、内容不可见
- 🎁 **新解锁**：卡片变成礼物盒，轻点「拆开」才能看到内容（开盲盒体验）
- ✨ **已拆开**：展示完整信息（购物清单、甜品地图、菜单等）
- ⏰ **自动解锁**：到达预设时间自动变礼物盒
- 🔧 **手动解锁**：通过管理面板或修改配置提前解锁

---

## 二、文件结构

```
birthday-surprise/
├── index.html      （34KB — 完整页面，含 CSS + JS，可独立运行）
├── config.json     （5KB  — 配置文件，你只需改这个）
└── README.md       （本文件）
```

**设计原则**：零依赖。不引用任何外部 CSS/JS/字体库，单 HTML 文件包含所有逻辑。

---

## 三、config.json 完整说明

### 3.1 顶层字段

| 字段 | 类型 | 说明 |
|------|------|------|
| `birthdayStar` | string | 女主角昵称 |
| `birthdayDate` | string | 生日日期（如 `"2026-07-07"`），仅展示用 |
| `heroMessage` | string | 页面顶部的标题文案，`\n` 换行 |
| `finalMessage` | string | 全部活动拆完后显示的结尾文案 |
| `activities` | array | 5 个活动卡片，顺序即展示顺序 |

### 3.2 每个 activity 的字段

| 字段 | 类型 | 说明 |
|------|------|------|
| `id` | string | 唯一标识，如 `"shopping-spree"` |
| `icon` | string | 卡片图标 emoji |
| `title` | string | 活动名称 |
| `subtitle` | string | 副标题（一行） |
| `unlockTime` | string | ISO 时间格式，到点自动解锁。如 `"2026-07-07T08:00:00"` |
| `forceUnlocked` | boolean | `true` = 无论到不到时间都解锁（手动覆盖） |
| `color` | string | 卡片图标背景色（HEX） |
| `content` | object | 解锁后展示的内容，见下方 |

### 3.3 content 内容类型

根据活动类型不同，`content` 有四种写法：

**A. 购物清单型**（购物车心愿单）
```json
"content": {
  "heroEmoji": "🛍️",
  "summary": "一句话总结",
  "items": [
    { "name": "商品名", "emoji": "👗", "note": "备注" }
  ],
  "closingLine": "结尾文案"
}
```

**B. 地图打卡型**（甜品上海大作战）
```json
"content": {
  "heroEmoji": "🗺️",
  "summary": "一句话总结",
  "stops": [
    { "name": "店名", "area": "区域", "mustTry": "必点", "emoji": "🍮" }
  ],
  "mapNote": "地图说明文字",
  "closingLine": "结尾文案"
}
```

**C. 详情卡片型**（攀岩 / 音乐会）
```json
"content": {
  "heroEmoji": "🧗‍♀️",
  "summary": "一句话总结",
  "details": [
    { "label": "标签", "value": "内容", "emoji": "📍" }
  ],
  "closingLine": "结尾文案"
}
```

**D. 菜单型**（西班牙晚餐）
```json
"content": {
  "heroEmoji": "🕯️",
  "summary": "一句话总结",
  "menu": [
    { "course": "前菜", "dishes": "菜名", "emoji": "🍖" }
  ],
  "wineNote": "酒水备注",
  "closingLine": "结尾文案"
}
```

---

## 四、日常操作指南

### 4.1 更新内容并上线

**方法一：直接在 GitHub 网页改（最简单）**
1. 打开 https://github.com/RobinSpin77/birthday-surprise/blob/main/config.json
2. 点右上角 ✏️ 编辑
3. 修改内容 → 点绿色「Commit changes」按钮
4. 等 30~60 秒，刷新页面链接即可看到更新

**方法二：本地改完用命令行推送**
```bash
cd "E:/document/Core Document/微信公众号运营/try/birthday-surprise"
# 修改 config.json 后...
git add config.json
git commit -m "更新活动内容"
git push
```

### 4.2 手动提前解锁某个活动

**方法一（远程生效）**：在 config.json 中把对应活动的 `forceUnlocked` 改为 `true`，commit 推送。她刷新页面就能看到新礼物盒。

**方法二（本地测试）**：在自己手机上打开页面 → 快速点右下角 🔑 三次 → 弹出管理面板 → 点「锁定中」按钮切换。

> ⚠️ 管理面板的解锁**仅在你当前设备生效**（存 localStorage）。要让你女朋友也看到，必须用方法一改 config.json。

### 4.3 全部重置（测试用）

在浏览器控制台执行：
```js
localStorage.clear(); sessionStorage.clear(); location.reload();
```

---

## 五、技术架构

### 5.1 解锁逻辑（三重判断，任一满足即解锁）

```
isUnlocked = hash覆盖 OR forceUnlocked OR 当前时间 >= unlockTime
```

优先级：管理面板手动 > 配置文件 forceUnlocked > 预设时间

### 5.2 状态流转

```
locked ──(解锁条件满足)──→ unlocked-unopened ──(点击拆开)──→ unlocked-opened
  🔒                           🎁                              ✨
  标题模糊                     礼物盒遮罩                       完整内容展开
  倒计时显示                   轻点可拆                         进度条+1
```

### 5.3 数据存储

| 存储 | 内容 | 用途 |
|------|------|------|
| `config.json` | 活动配置、解锁时间 | 远程部署，所有设备一致 |
| `localStorage` | `birthday-opened`（已拆列表）、`birthday-manual-overrides`（手动覆盖） | 设备本地状态 |
| `sessionStorage` | `birthday-known-unlocked` | 追踪新解锁的卡片（用于动画） |

### 5.4 浏览器兼容

- ✅ iOS Safari
- ✅ 微信内置浏览器
- ✅ Android Chrome
- ✅ 桌面端 Chrome / Edge / Safari

---

## 六、设计规范

| 属性 | 值 |
|------|-----|
| 主色调 | `#FF7EB3` 暖粉 |
| 辅助色 | `#FFB347` 暖金、`#FFD4E0` 浅粉 |
| 背景 | 渐变 `#FFF0F3 → #FFF5F7` |
| 卡片圆角 | 20px |
| 特效 | Canvas 粒子（漂浮爱心/花瓣）、Canvas 彩纸（拆礼物时） |
| 动画 | CSS keyframes（浮动、脉冲、弹入、礼物摇动） |

---

## 七、常见问题

### Q: 她打开页面什么都没解锁？
A: 检查 `unlockTime` 是否已过当前时间。时区按浏览器本地时间计算。

### Q: 想增加/减少活动？
A: 在 `config.json` 的 `activities` 数组中增删条目，页面自动适配。支持 1~N 个。

### Q: 想换图标/颜色？
A: 改 `icon`（emoji）和 `color`（HEX 色值）。每个活动可独立配色。

### Q: 管理面板打不开？
A: 在 1 秒内快速点击右下角 🔑 三次。

### Q: 部署后页面白屏？
A: 检查浏览器控制台（F12）看 `config.json` 是否 404。确保两个文件都在仓库根目录。

---

## 八、继续请 Claude 帮忙

把这个项目发给 Claude 时，附上这段话可以让 Claude 快速理解：

> 这是一个生日惊喜 H5 页面项目，文档见 README.md。请先阅读 README 和 config.json，然后帮我……

常用指令示例：
- 「帮我把甜品店信息改成真实的」
- 「再加一个活动——温泉 Spa」
- 「把所有解锁时间改成 7/7」
- 「帮我重新部署到线上」

---

*最后更新：2026-07-05*
