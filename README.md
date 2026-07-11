# 🎂 生日惊喜页面 — 项目文档

> **🔗 分享链接**：https://robinspin77.github.io/birthday-surprise/
> **GitHub 仓库**：https://github.com/RobinSpin77/birthday-surprise
> **部署**：GitHub Pages（main 分支自动部署）

---

## 一、项目概述

生日惊喜 H5 页面，包含：

- 🕯️ **生日倒计时**（天/时/分/秒，到点变祝福）
- 🐖🐕 **飞猪飞狗**（Canvas 粒子，🪽 双翼扑动，靠近触发爱心爆发）
- 📊 **解锁进度条**
- 5 张活动卡片：
  1. 🛒 购物车心愿单（快递追踪）
  2. 🧗 人生新技能解锁（攀岩）
  3. 🍰 甜品上海大作战（表格 + 高德地图路线）
  4. 🎵 音乐会（极光管弦乐团）
  5. 🥘 布朗石西班牙餐厅晚餐

---

## 二、核心功能

| 功能 | 说明 |
|------|------|
| 卡片三态 | locked(模糊+倒计时) → 🎁礼物盒 → ✨完整内容 |
| 解锁逻辑 | `forceUnlocked` 或当前时间 ≥ `unlockTime` |
| 管理面板 | 长按 🎂 5 秒 + 再点一下，或 URL 加 `?admin` |
| 购物快递 | 每件物品 status: 已到小笨家/运输中/未发货，前两种模糊 |
| 甜品地图 | 高德瓦片 + 搜索框输入 → 高德 Input Tips API 实时定位 → 📍标记连线 |
| 飞猪飞狗 | 🐖🐕 正弦波飞行 + 🪽 双翼 + 💨 尾迹 + <120px 触发爱心彩蛋 |

---

## 三、config.json 字段

### 顶层

| 字段 | 说明 |
|------|------|
| `birthdayStar` | 昵称 |
| `birthdayDate` | 生日日期，用于倒计时 |
| `heroTitle` | 顶部大字 |
| `heroMessage` | 副文案 |
| `amapKey` | 高德 Web API Key（甜品地图搜索用） |
| `finalMessage` | 全部拆完后文案 |
| `activities` | 活动数组 |

### activity 字段

| 字段 | 说明 |
|------|------|
| `id`, `icon`, `title`, `subtitle` | 基础信息 |
| `unlockTime` | ISO 时间，如 `"2026-07-09T12:00:00"` |
| `forceUnlocked` | true = 立即解锁 |
| `color` | 图标背景色 |
| `content` | 见下方 |

### content 四种类型

**A. 购物清单型** — 支持快递追踪
```json
"content": {
  "heroEmoji": "🛍️", "summary": "一句",
  "items": [
    { "name":"商品", "emoji":"👗", "note":"备注", "status":"已到小笨家" }
  ],
  "closingLine": "收尾"
}
```
status: `"已到小笨家"` → 完全可见 ✅ / `"运输中"` → 模糊 🚚 / `"未发货"` → 模糊 📦

**B. 可编辑表格型**（甜品逛吃）
```json
"content": {
  "heroEmoji": "🗺️", "summary": "一句",
  "table": { "columns":["店名","品类","评价"], "addLabel":"➕ 记录一家店" },
  "closingLine": "收尾"
}
```
表格下方自动渲染高德地图，搜索框输入店名 → 实时定位 → 标记连线。

**C. 详情卡片型**（攀岩/音乐会）
```json
"content": {
  "heroEmoji": "🧗‍♀️", "summary": "一句",
  "details": [{ "label":"标签", "value":"内容", "emoji":"📍" }],
  "closingLine": "收尾"
}
```

**D. 纯文本型**（晚餐）
```json
"content": {
  "heroEmoji": "🕯️", "summary": "氛围描述"
}
```
不写 details/menu/items 就是纯文字卡片。

---

## 四、日常操作

### 更新上线
```bash
cd "E:/document/Core Document/微信公众号运营/try/birthday-surprise"
git add config.json && git commit -m "更新" && git push
```
等 30~60 秒 GitHub Pages 自动部署。

### 管理面板
- 长按 🎂 5 秒 + 再点一下
- 或 URL 加 `?admin`

### 快递更新
改 `config.json` 中对应 item 的 `status` 字段，推上线。

### 重置测试
```js
localStorage.clear(); sessionStorage.clear(); location.reload();
```

---

## 五、技术栈

| 层 | 技术 |
|------|------|
| 页面 | 零依赖单 HTML（CSS + Vanilla JS + Canvas） |
| 地图 | Leaflet 1.9.4 + 高德瓦片 + 高德 Input Tips API |
| 存储 | config.json(远程) + localStorage(本地状态) + sessionStorage(动画) |
| 部署 | GitHub Pages |
| 兼容 | iOS Safari / 微信 / Android Chrome / 桌面 |

---

## 六、更新日志

| 日期 | 内容 |
|------|------|
| 7/5 | 初始版本，5 卡片，GitHub Pages |
| 7/5 | 生日倒计时 + heroTitle 配置化 |
| 7/5 | 锁定模糊 + 管理面板长按蛋糕 |
| 7/5 | 活动倒计时 dd-hh-mm 格式 |
| 7/5 | 🐖🐕 飞猪飞狗双翼飞行 |
| 7/5 | 📦 快递追踪三种状态 |
| 7/5 | 甜品可编辑表格 + 高德真实地图路线 |
| 7/6 | 管理面板 5 秒 + 点击 |
| 7/6 | 模糊加厚(10px/8px/7px) |
| 7/9 | 活动对调：音乐会→晚餐，内容更新 |
| 7/10 | 高德 Input Tips API 真实 POI 搜索定位 |

---

*最后更新：2026-07-11*
