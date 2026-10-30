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
      "version": "1.0.0",
      "swatch": {
        "light": { "--ui-canvas": "#f5f9fe", "--ui-surface": "#ffffff", "--ui-fg": "#1c2026", "--ui-accent": "#00a56e" },
        "dark": { "--ui-canvas": "#0a0b0d", "--ui-surface": "#0c0c0d", "--ui-fg": "#edeff2", "--ui-accent": "#32f08c" }
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

| 主题 | 说明 |
| --- | --- |
| `trae-cn`（Trae 国内版） | 近黑灰底 `#0a0b0d`、品牌绿 `#32f08c`、蓝色链接 `#64b4ff`、输入框蓝色焦点；浅色底 `#f5f9fe` 配 `#00a56e`；四个强调色预设：绿 / 蓝 / 紫 / 琥珀 |
| `clay-glow`（陶光） | 暖纸底 `#fbf9f4` 配陶土强调 `#b85434`（浅色那面）；深色是暖近黑 `#191713` 配铜橙 `#e08962`。236 个浅色令牌、102 个深色令牌，语义色 / 图表色 / 控件一套齐全 |

## 许可

Apache-2.0。主题只包含颜色与尺寸这类取值，不含代码。
