# Adelie Themes

Adelie 的**主题库**：一份目录（`index.json`）加上它点名的主题文件（`themes/*.json`）。Adelie 的外观页把这份目录画成一组卡片，点一下就由服务端把那份文件取回来、按主题格式读一遍、写进本机的 `themes/` 目录——和手动把文件放进去是同一件事。

同一个仓库既是 npm 包（`@lmliheng/adelie-themes`），也是一份 git 仓库；内容完全一样，只是取法不同。

## 默认地址

```
https://registry.npmmirror.com/@lmliheng/adelie-themes/latest/files/
```

Adelie 在这个地址下面读 `index.json`，再按条目里的 `file` 拼出主题文件的地址。想换镜像、换私有部署，设 `ADELIE_THEME_REGISTRY` 环境变量指向自己的目录即可（结尾斜杠可有可无，下一层必须是 `index.json`）。

## 目录格式（`index.json`）

```json
{
  "schemaVersion": 1,
  "name": "Adelie Themes",
  "updatedAt": "2026-10-29",
  "themes": [
    {
      "id": "trae-cn",
      "name": "Trae CN",
      "nameZh": "Trae 国内版",
      "author": "lmliheng",
      "description": "Trae's near-black greys with its brand green…",
      "descriptionZh": "Trae 国内版的近黑灰底、品牌绿强调……",
      "file": "themes/trae-cn.json",
      "version": "1.1.0",
      "swatch": {
        "light": { "--ui-canvas": "#ffffff", "--ui-surface": "#ffffff", "--ui-fg": "#1a1a1a", "--ui-accent": "#0fdc78" },
        "dark": { "--ui-canvas": "#1a1b1d", "--ui-surface": "#202123", "--ui-fg": "#d1d3db", "--ui-accent": "#32f08c" }
      }
    }
  ]
}
```

| 字段 | 必填 | 说明 |
| --- | --- | --- |
| `schemaVersion` | 否 | 现在只有 `1`；写了别的版本号整份目录会读不出来 |
| `name` / `updatedAt` | 否 | 目录自己的名字与日期，界面上显示 |
| `themes[].id` | 是 | 小写字母开头，`a-z0-9-`，最长 32；和主题文件里的 `id` **必须一致** |
| `themes[].name` | 是 | 显示名（1–60 字） |
| `themes[].nameZh` | 否 | 中文显示名，不写就用 `name` |
| `themes[].author` | 是 | 作者（1–60 字） |
| `themes[].description` / `descriptionZh` | 否 | 卡片上的一句话（≤280 字） |
| `themes[].file` | 是 | **仓库内**的相对路径；不允许绝对地址、`..`、反斜杠 |
| `themes[].version` | 否 | 主题文件自己的版本号，卡片上显示 |
| `themes[].swatch` | 否 | 卡片小色板：`light` / `dark` 各四个名字，只能是 `--ui-canvas` / `--ui-surface` / `--ui-fg` / `--ui-accent` |

目录是**整体校验**的：任何一条不合规，整份目录都会被拒绝并列出全部原因，而不是悄悄少几条——少一条的目录看起来像「作者刚加的没生效」，谁也定位不到。

## 主题格式（`themes/<id>.json`）

就是 Adelie 的外置主题文件格式，见 Adelie 仓库的 `examples/themes/warm-paper.json`。要点：

- `schemaVersion`、`id`、`name`（+ 可选 `nameZh`）；
- `light` 是一整组令牌值，`dark` 只写和浅色不同的；
- 值只能是单个 CSS 值：不能带 `;` `{` `}` `\` `<` `>` `@` 或换行，也不能是 `url(…)` / `image-set(…)`；
- `accents` 可选，每个是 `{ id, light: {…}, dark: {…} }`，给外观页的强调色预设；
- 文件上限 256 KB。

## 加一个主题

1. 写 `themes/<id>.json`，里面的 `id` 与文件名一致；
2. 在 `index.json` 的 `themes` 里加一条，`file` 指过去；
3. 改 `package.json` 的版本号（npm 同一版本不能重发），发布：

```bash
npm publish
git add -A && git commit -m "feat: 新增 <名字> 主题" && git push
```

npmmirror 跟着 npm 更新，Adelie 那边的目录缓存是五分钟。

## 已有的主题

每份主题都是 236 个浅色令牌的全量取值，深色只写与浅色不同的那些（`dark` 的条数就是「这套主题深色改了哪些」）。

| 主题 | 说明 |
| --- | --- |
| `cyber`（赛博朋克） | 近黑底上的霓虹洋红与青，**两种模式都是夜**：尖角、等宽、标题全大写，入场带一点故障感（`--ui-chart-curve` 也换成台阶）。236 浅 / 89 深 |
| `doodle`（手绘涂鸦） | 暖纸底配蜡笔橙，2px 手绘粗细的描边，动效带点弹。236 浅 / 88 深 |
| `clay-glow`（陶光） | 暖纸底 `#fbf9f4` 配陶土强调 `#b85434`（浅色那面）；深色是暖近黑 `#191713` 配铜橙 `#e08962`。236 浅 / 102 深 |
| `skeuo`（拟物化） | 暖纸与帆布质感配斜面：每个凸起的东西上面一道亮线、下面一层投影，菜单带磨砂。236 浅 / 92 深 |

`cyber`、`doodle`、`skeuo` 三套是**按「长什么样」而不是「学哪个产品」**做的：风格的名字只是名字，取值照着这个风格该有的样子定——颜色、圆角、描边粗细、阴影、字体栈、动效时长与缓动。

## 主题能表达什么、不能表达什么

一套主题**只是 236 个 `--ui-*` 的取值**。组件只读这些名字、从不问当前是哪套主题（`packages/ui/test/no-theme-reads.test.ts` 钉着这条），所以外置主题能重画整个应用而不用重建前端——代价就是下面这些边界，做风格主题时会直接撞上：

- **能表达**：颜色（每一层、每一档文字、五种状态色、图表色、代码块、滚动条、遮罩、选中、焦点圈）、圆角、描边粗细、字号行高字重字距、字体栈、阴影（任意 CSS 阴影串）、动效时长与缓动曲线，以及面板的毛玻璃参数。
- **不能表达**：**图案、纹理、插画**。手绘涂鸦没有它该有的手抖笔触，赛博朋克也没有真的一点点扫描线纹理——文件里写不下图，`url()` 也被格式禁止。
- **画布只能是一个纯色**。应用用 `background-color` 画 `--ui-canvas`，所以渐变放进去会被浏览器直接丢掉（赛博朋克因此只用纯色底配霓虹色）。
- **十六个 `ui-*` 风格钩子（glass / shell / field / live / notice / chart / scrim / stream / …）的配方写在自带主题的 CSS 里**，按主题 id 限定选择器。外置主题没有 CSS，只有值，所以钩子对它是空操作：`--ui-glass-blur`、`--ui-shell-*` 这些值会被写进样式表，但当前没有规则读它们。要真正拿到那种效果，得在 `packages/ui/src/themes/` 里添一个主题文件，不是加一份数据。
- 顺带一条：**主题文件里一个值最多 120 个字符、单行**，不能有 `;` `{` `}` `\` `<` `>` `@` 与换行。自带主题里那两个跨行的字体栈（`--ui-font-sans` / `--ui-font-mono`）因此不能原样抄，每套风格都自己写了等宽字体栈。


## 取值来源

主题里每个颜色都能追到出处，不是「照着感觉配的」：

- **三个风格（`cyber` / `doodle` / `skeuo`）**：没有产品可抄，所以每一套都是「把决定写下来」——画布、墨色、一个强调色、五个状态色、八条图表色，加上圆角/字体/动效这一族非颜色取值，都写在生成器的 `theme_styles.py` 里；其余角色（各种 hover、浅底、发丝线、状态底、缓存色）由同一套规则从这些值推出来，不另配颜色。
- **`clay-glow`**：暖纸与陶土这一族的取值——浅色纸面 `#fbf9f4`、陶土强调 `#b85434`，深色暖近黑 `#191713` 配铜橙 `#e08962`。

四套主题都跑过三关：**对比度**（正文、弱化文字、状态文字与强调色上的文字 ≥ 4.5:1，不足的按「朝画布另一端混合」推到位）、**按应用的读法校验**（`readExternalTheme` + `@adelie/ui` 的 236 个令牌名一个不少、没有不认识的）、以及**真浏览器里逐条比对**——文件里写多少个名字，浏览器就得算回同样的值（4 套 × 浅深 = 8 条全过）。

## 许可

Apache-2.0。主题只包含颜色与尺寸这类取值，不含代码。
