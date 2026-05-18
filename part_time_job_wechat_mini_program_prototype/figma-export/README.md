# 三方关键流程原型 · Figma 可编辑导出

本目录是把 `full-flow-prototype.html` 转成 Figma 可分解资源的成品。

## 目录结构

```
figma-export/
├─ svg/        ← 主交付物。结构化 SVG,每屏一个文件,图层带命名,文本可编辑
└─ png/        ← 同名 PNG (390×736 @2x),做保真参考 / 缩略图用
```

## 7 张屏(按三方分组)

### 应聘方
| # | 文件 | 内容 |
|---|------|------|
| 01 | `01-应聘方-报名闭环.svg` | #11 收到匹配推送 + 推荐卡列表(全屏含滚动内容) |
| 02 | `02-应聘方-录用通知.svg` | #19a 录用通知 · T3 24h 应答倒计时 |
| 03 | `03-应聘方-成单完工.svg` | #20–22 成单 / 完工时间线 |

### 招聘方
| # | 文件 | 内容 |
|---|------|------|
| 04 | `04-招聘方-发布招募令.svg` | #9 发一条招募令 · 草稿表单 |
| 05 | `05-招聘方-确认入选名单.svg` | #18 确认中介提交的 ranked roster |
| 06 | `06-招聘方-完工确认.svg` | T11 7 天窗口内确认完工 |

### 中介方
| # | 文件 | 内容 |
|---|------|------|
| 07 | `07-中介方-招募令列表.svg` | cream 钻取链首屏 · 今日招募 ISSUE 042 |

## 怎么把 SVG 弄进 Figma

**方法 A · 直接拖拽(最简单)**
1. 打开 Figma 桌面端或网页端,新建一个 frame
2. 把 SVG 文件直接拖进画布
3. 选中导入的 group,按 `Cmd/Ctrl + Shift + G` (Ungroup) 一层一层分解
4. 文本是真正的 `<text>` 节点 → 双击可编辑

**方法 B · html.to.design 插件**(适合需要 Auto Layout 的情况)
- 用 [html.to.design](https://www.figma.com/community/plugin/1159123024924461424/html-to-design) 把原 HTML 也导一份,跟 SVG 对照排版

## 已知取舍

- **图层命名**:每个 `<div>`/`<button>`/`<span>` 在 SVG 里成为带 `data-name` 的 `<g>` group,Figma 会把它显示为图层名。多数命名只是 `div`(因为 HTML 里没有更语义化的名字)
- **字体**:SVG 引用了 `DM Serif Display` / `DM Serif Text` / `EB Garamond` / `Playfair Display` / `JetBrains Mono` / `Noto Serif SC`,Figma 里需要保证这些字体已安装,否则会回退到默认字体且字宽对不上
- **文本换行**:HTML 自动换行的多行文本,在 SVG 里被拆成多个 `<text>` 元素(每行一个),所以一段话进 Figma 后会是几条分开的文本
- **图标**:状态栏和底栏图标已转成内联 SVG `<path>` / `<rect>`,在 Figma 里可独立选中编辑
- **内容溢出**:某些屏(#11、#18、#9 等)的内容高度超过 736px(iOS 26 frame 高),SVG 的 viewBox 是 390×736,溢出的部分仍在 SVG 内但默认不显示 —— 在 Figma 里把 frame 高度调大就能看到全部内容
- **AI 标注框**:`v2 · 本版不做` 等说明文字全部保留为可编辑文本

## 重新生成

如果以后改了原型想重新出 SVG:重开 `prototype.html`,在控制台里加载 `tools/dom-to-svg.js`,对每个 `.phone-block > 第二个子元素` 调用 `window.domToCleanSVG(el, { title: '...' })` 即可。
