# 🎂 生日惊喜页面 — 项目文档

> **🔗 分享链接**：https://birthday-77.netlify.app （主部署，国内可访问）
> **备用直连**：https://robinspin77.github.io/birthday-surprise/
> **GitHub 仓库**：https://github.com/RobinSpin77/birthday-surprise
> **部署**：Netlify（主） + GitHub Pages（备）

---

## 一、项目概述

一个 H5 生日惊喜页面，女朋友点开链接后能看到：

- 🕯️ **生日倒计时**（天/时/分/秒，每秒跳动，到 7/9 0 点变生日祝福）
- 📊 **解锁进度条**（已完成 x/5）
- 🐖🐕 **飞猪 + 飞狗**（Canvas 粒子动画，侧面站立 + 🪽 双翼，靠近触发爱心彩蛋）
- 5 个活动卡片：
  1. 🛒 购物车心愿单
  2. 🧗 人生新技能解锁（攀岩）
  3. 🍰 甜品上海大作战
  4. 🥘 共进西班牙晚餐
  5. 🎵 解锁音乐会体验

**核心机制**：
- 🔒 **锁定状态**：图标+标题+副标题全部模糊，倒计时显示，内容不可见
- 🎁 **新解锁**：卡片变礼物盒，轻点「拆开」才看到内容（开盲盒体验）
- ✨ **已拆开**：展示完整信息（购物清单、甜品地图、菜单等）
- ⏰ **自动解锁**：到达预设时间自动变礼物盒
- 🔧 **手动解锁**：长按蛋糕 🎂 3 秒 或 URL 加 `?admin` 打开管理面板

**安全设计**：
- 锁定卡片图标+标题+副标题全部模糊，无法辨认
- 管理面板无可见入口（长按蛋糕 3 秒 / `?admin` 参数），她不可能发现
- 分享链接用 Netlify 域名，不暴露 GitHub 仓库
- 活动倒计时 >1 天显示 `dd-hh-mm` 格式

---

## 二、文件结构

```
birthday-surprise/          ← 主项目（Git 仓库）
├── index.html              ← 完整页面（零依赖单文件）
├── config.json             ← 配置文件（你只需改这个）
└── README.md               ← 本文件

birthday-redirect/          ← Netlify 部署目录
├── index.html              ← 主页面副本
├── config.json             ← 配置文件副本
└── netlify.toml            ← Netlify 构建配置（跳过 hugo）
```

**设计原则**：零外部依赖，单 HTML 文件包含全部 CSS/JS/Canvas。

---

## 三、config.json 完整说明

### 3.1 顶层字段

| 字段 | 类型 | 说明 |
|------|------|------|
| `birthdayStar` | string | 女主角昵称 |
| `birthdayDate` | string | 生日日期，用于倒计时。如 `"2026-07-09"` |
| `heroTitle` | string | 页面顶部大字标题 |
| `heroMessage` | string | 标题下方副文案，`\n` 换行 |
| `finalMessage` | string | 全部拆完后显示的结尾文案 |
| `activities` | array | 活动卡片列表，顺序即展示顺序 |

### 3.2 每个 activity 的字段

| 字段 | 类型 | 说明 |
|------|------|------|
| `id` | string | 唯一标识 |
| `icon` | string | 卡片图标 emoji |
| `title` | string | 活动名称 |
| `subtitle` | string | 副标题 |
| `unlockTime` | string | ISO 时间，到点自动解锁。如 `"2026-07-07T08:00:00"` |
| `forceUnlocked` | boolean | `true` = 无视时间立即解锁 |
| `color` | string | 图标背景色 (HEX) |
| `content` | object | 解锁后展示的内容，见下方 |

### 3.3 content 四种类型

**A. 购物清单型**（购物车心愿单）—— 支持快递追踪
```json
"content": {
  "heroEmoji": "🛍️", "summary": "一句话",
  "items": [
    {
      "name": "商品名", "emoji": "👗", "note": "备注",
      "status": "已到小笨家"   // ← "已到小笨家" | "运输中" | "未发货"
    }
  ],
  "closingLine": "结尾"
}
```

| status 值 | 显示效果 |
|-----------|---------|
| `"已到小笨家"` | 名称+备注完全可见，绿色「已送达 ✅」徽章 |
| `"运输中"` | 名称隐藏为「运输中…」，图标模糊，橙色徽章 |
| `"未发货"` | 名称隐藏为「待签收…」，图标模糊，灰色徽章 |

**B. 打卡地图型**（甜品上海大作战）
```json
"content": {
  "heroEmoji": "🗺️", "summary": "一句话",
  "stops": [{ "name": "店名", "area": "区域", "mustTry": "必点", "emoji": "🍮" }],
  "mapNote": "地图说明", "closingLine": "结尾"
}
```

**C. 详情卡片型**（攀岩 / 音乐会）
```json
"content": {
  "heroEmoji": "🧗‍♀️", "summary": "一句话",
  "details": [{ "label": "标签", "value": "内容", "emoji": "📍" }],
  "closingLine": "结尾"
}
```

**D. 菜单型**（西班牙晚餐）
```json
"content": {
  "heroEmoji": "🕯️", "summary": "一句话",
  "menu": [{ "course": "前菜", "dishes": "菜名", "emoji": "🍖" }],
  "wineNote": "酒水备注", "closingLine": "结尾"
}
```

---

## 四、日常操作

### 4.1 分享链接

| 链接 | 用途 | 暴露 GitHub？ |
|------|------|:--:|
| `https://birthday-77.netlify.app` | **发给她** | ❌ |
| `https://robinspin77.github.io/birthday-surprise/` | 自测 | ✅ |

### 4.2 更新内容上线

**方法一：GitHub 网页改**
1. 打开 https://github.com/RobinSpin77/birthday-surprise/blob/main/config.json
2. 点 ✏️ 编辑 → 修改 → Commit changes
3. 告诉 Claude「帮我把更新同步到 Netlify」

**方法二：告诉 Claude**
> 「帮我更新 config.json 并部署到 Netlify」

Claude 会：改 config.json → git push → cp 到 redirect → `netlify deploy --prod`

### 4.3 打开管理面板

| 方式 | 操作 |
|------|------|
| 手机 | 长按蛋糕 🎂 **3 秒**不松 |
| 电脑 | 鼠标按住蛋糕 🎂 3 秒 |
| 万能 | URL 后加 `?admin`，如 `birthday-77.netlify.app?admin` |

> ⚠️ 面板解锁仅本设备生效（localStorage）。要让她也看到，必须改 `forceUnlocked: true` 并部署。

### 4.4 重置测试

```js
localStorage.clear(); sessionStorage.clear(); location.reload();
```

---

## 五、技术架构

### 5.1 解锁逻辑

```
isUnlocked = forceUnlocked OR 当前时间 >= unlockTime
```

### 5.2 卡片状态流转

```
locked ──(解锁)──→ unlocked-unopened ──(点击拆开)──→ unlocked-opened
  🔒                   🎁                              ✨
```

### 5.3 动画系统

| 层 | 技术 | 内容 |
|------|------|------|
| 粒子背景 | Canvas 2D | 25 颗漂浮爱心/花瓣 (💕✨🌸💖) |
| 飞猪飞狗 | Canvas 2D | 🐖🐕 + 🪽 双翼扑动 + 💨 尾迹 + 靠近爱心爆发 |
| 彩纸 | Canvas 2D | 拆礼物时触发，80 片彩纸飘落 |
| 卡片动画 | CSS keyframes | 浮动、脉冲、弹入、礼物摇动 |

### 5.4 浏览器兼容

- ✅ iOS Safari / 微信内置浏览器 / Android Chrome / 桌面 Chrome/Edge/Safari

---

## 六、设计规范

| 属性 | 值 |
|------|-----|
| 主色调 | `#FF7EB3` 暖粉 |
| 辅助色 | `#FFB347` 暖金 |
| 卡片圆角 | 20px |
| 锁定模糊 | 图标 blur(5px)、标题 blur(6px)、副标题 blur(5px) |
| 倒计时格式 | ≥1天: `X天X时X分`，<1天: `X时X分`，<1时: `X分X秒` |

---

## 七、常见问题

| Q | A |
|----|----|
| 她打开什么都没解锁？ | 检查 `unlockTime` 是否已过，时区按浏览器本地时间 |
| 想增减活动？ | 改 `activities` 数组，页面自动适配 |
| 管理面板打不开？ | 长按蛋糕 3 秒，或加 `?admin` |
| Netlify 部署失败？ | 检查 `netlify.toml` 是否在目录中 |

---

## 八、请 Claude 帮忙

> 这是一个生日惊喜 H5 项目，文档见 README.md。帮我……

常用指令：
- 「帮我把甜品店信息改成真实的」
- 「再加一个活动」
- 「解锁攀岩活动」
- 「更新部署到 Netlify」

---

*最后更新：2026-07-05*

## 九、更新日志

| 日期 | 内容 |
|------|------|
| 7/5 | 初始版本，5 卡片，GitHub Pages |
| 7/5 | 生日倒计时 |
| 7/5 | `heroTitle` 配置化 |
| 7/5 | 锁定图标模糊 + 管理面板长按蛋糕 |
| 7/5 | Netlify 直接托管（非 iframe） |
| 7/5 | 活动倒计时 dd-hh-mm 格式 |
| 7/5 | 🐖🐕 飞猪飞狗双翼飞行 + 靠近彩蛋 |
| 7/5 | 管理面板升级：长按 5 秒 + 再点一下 |
| 7/5 | 锁定模糊加厚：图标 blur(10px)、标题 blur(8px) |
| 7/5 | 📦 快递追踪：购物清单支持 未发货/运输中/已到 三种状态 |
