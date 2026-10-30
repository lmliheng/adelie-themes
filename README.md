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
| `trae-cn`（Trae 国内版） | Trae 客户端的配色：底 `#1a1b1d`、面板 `#222427`、品牌绿 `#32f08c`、设计蓝 `#80c1ff`；浅色是白底配 `#0fdc78` 绿按钮。236 浅 / 85 深，9 个强调色预设（琥珀 / 蓝 / 珊瑚 / 青 / 柠 / 品红 / 石板 / 青绿 / 紫），取自它自己那套 accent 令牌 |
| `clay-glow`（陶光） | 暖纸底 `#fbf9f4` 配陶土强调 `#b85434`（浅色那面）；深色是暖近黑 `#191713` 配铜橙 `#e08962`。236 浅 / 102 深 |
| `vscode`（VS Code 现代） | VS Code 自己的两层外壳：`#181818` 面板包着 `#1f1f1f` 编辑区，蓝 `#0078d4`，2px 圆角，13px 界面字，无强调色预设。236 浅 / 81 深 |
| `kimi`（Kimi） | 暖白底 `#fbfaf9` 侧栏配白内容区，主按钮是 `rgba(0,0,0,.9)` 的黑底白字（深色反之），8–16px 圆角，系统字体栈，6 个强调色预设。236 浅 / 81 深 |

## 取值来源

主题里每个颜色都能追到出处，不是「照着感觉配的」：

- **`vscode`**：`microsoft/vscode` 的 `extensions/theme-defaults/themes/{dark,light}_modern.json`（界面色）与 `{dark,light}_{plus,vs}.json`（语法色，用作图表色）；圆角、字号、间距按 VS Code 自己的观感（2px 直角、13px 界面字、系统字体栈），阴影是推导值。
- **`kimi`**：线上站点的自定义属性，浅深两套都在真浏览器里读过（`--Bg-*`、`--Labels-*`、`--Fills-*`、`--Colors-*`、`--Others-*`、`--Syntax-*`）。主按钮的黑/白底是实测计算值。
- **`trae-cn`**：Trae 客户端的令牌（`--bg-bg-*`、`--text-text-*`、`--border-border-*`、`--status-*`、`--accent-accent-*`），浅深两套；另外用 `docs.trae.cn` 里 IDE 截图核对过底与面板色 —— 官网首页那套近黑 `#0a0b0d` 是营销页，不是客户端。

三套主题都跑过：色板、对比度（正文与强调色上的文字 ≥ 4.5:1，不足的按「朝画布另一端混合」推到位，推导出的值在各主题注释里注明），以及「真浏览器里逐条比对」——文件里写 236 个名字，浏览器就得算回同样的 236 个值。

## 许可

Apache-2.0。主题只包含颜色与尺寸这类取值，不含代码。
