# 单页工具开发约定

本文描述全部工具页的公共约定：硬约束、统一 UI、页面骨架与已知平台结论。
按本文写出的页面应与现有工具完全一致。带样板文件的对照关系：

- **最完整样板：`audio-convert.html`** —— 集全部惯用件于一页：拖放选文件、卡片、
  按钮状态机、loading、footer 状态栏、双 URL 试听/下载、页尾格式说明。
- 复杂两栏类样板：`image-watermark.html`（首选——标准两栏 grid、`.group-title`
  分区、`.slot` 文件槽、`.mini`/`.primary`/`.hint-line` 俱全）；次选 `video-frame.html`。

共同提供某个样板时，结构、命名与注释风格以它为准；未提供时按本文的数值与模板执行。

---

## 一、硬约束（红线，违反即返工）

1. **单文件**：每个工具就是一个独立 `.html`，双击即可打开，无构建步骤、无依赖、可长期保存。
2. **零外部引用**：不用 CDN、不引 JS/CSS 库、不用网络字体（图标一律用 emoji）、不加载在线资源。
   页面上出现的所有功能——渲染器、解析器、编码器——要么手写，要么用浏览器原生能力。
3. **不经过服务器**：没有后端、不经任何第三方服务，所有计算/解码/编码在浏览器本地完成。
   任何页面都不得将用户数据发送到网络。隐私说明写在 header 的 hint 里是惯例。
4. **禁用追踪**：无 analytics、无外链统计、无 cookie。
5. **localStorage 只能存界面偏好**（如分栏比例），**绝不存用户内容**（不搞静默自动保存）。
6. **中文 UI**：界面文案、代码注释全部中文；注释讲「为什么」和坑，不复述代码。
7. 每个 `.html` 通过浏览器打开独立成立；`index.html` 是唯一的入口名录，其它工具页**不写**
   「← 返回工具箱」反向链接。
8. 脚本一律写进 `<script>` 的 IIFE 里（`(function () { "use strict"; … })();`），
   结束后对所有 `console.log` 调试残留清零。

---

## 二、UI 色板与字体（全站统一 token）

每个页面自带头部 `<style>`，:root 里原样写这一套（改这里即可整体换肤）：

```css
:root {
  --bg: #0e1116;
  --panel: #171c24;      /* 卡片 */
  --panel-2: #1e2632;    /* 按钮/次级表面 */
  --border: #2a3340;
  --text: #e8edf4;
  --muted: #8b97a8;
  --accent: #3dd68c;     /* 主题绿 */
  --accent-2: #2bb673;   /* hover */
  --warn: #ffb340;
  --radius: 10px;
  --mono: ui-monospace, SFMono-Regular, Menlo, Consolas, monospace;
  --sans: -apple-system, BlinkMacSystemFont, "Segoe UI", "PingFang SC",
          "Hiragino Sans GB", "Microsoft YaHei", sans-serif;
}
```

favicon 用内联 data:（深绿圆角方块 + 绿色 `>_`），每页相同，原样取这一行：

```html
<link rel="icon" href="data:image/svg+xml,%3Csvg xmlns='http://www.w3.org/2000/svg' viewBox='0 0 32 32'%3E%3Crect width='32' height='32' rx='6' fill='%23171c24'/%3E%3Ctext x='16' y='23' font-size='18' font-weight='bold' font-family='ui-monospace,Menlo,monospace' fill='%233dd68c' text-anchor='middle'%3E%3E_%3C/text%3E%3C/svg%3E" />
```

---

## 三、页面骨架（冷启动模板）

复用这一套骨架，新页面先抄后改：

```html
<!DOCTYPE html>
<html lang="zh-CN">
<head>
  <meta charset="utf-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1" />
  <title>工具名</title>
  <meta name="description" content="一句话说明，注明「纯前端处理/不上传」" />
  <link rel="icon" href="data:image/svg+xml,%3Csvg …%3E" />  <!-- 第二节给的完整一行 -->
  <style>  /* 上述 :root token + 下方组件样式 */  </style>
</head>
<body>
  <header>
    <h1>emoji + 工具名</h1>
    <span class="hint">一句话特性说明（隐私/能力）</span>
  </header>

  <div class="wrap">           <!-- 简单工具：窄列居中 max-width ~720px -->
    <p class="sub">说明与用法一两句。</p>
    <div class="drop">…</div>  <!-- 文件类工具：点击/拖放选文件 -->
    <div class="card">…</div>  <!-- 信息卡 / 操作卡 / 说明卡，card + card-title -->
  </div>

  <footer>
    <span class="dot"></span><span id="statusText">等待选择…</span>
  </footer>

  <script>(function () { "use strict"; /* … */ })();</script>
</body>
</html>
```

布局二选一：

- **简单工具**（信息/转换/查询类）：上述窄列居中（`width: min(720px, 100%); margin: 0 auto`），
  参考 `user-agent.html`、`audio-convert.html`。
- **复杂工具**（需要左右联动、实时预览）：两栏 grid
  `grid-template-columns: minmax(340px, 380px) minmax(0, 1fr)`，左栏控件/右栏预览，
  参见 `video-frame.html`、`image-watermark.html`。
  关键一条：两栏各自独立滚动时必须写 `grid-template-rows: minmax(0, 1fr)` 收敛行高。
- **两栏形态的分区体系**（与单列的 `.card` 并行，见 `image-watermark.html`）：
  左栏用 `.panel` 包裹，内部以 `.group` 分块（相邻块 `margin-top: 18px`），
  每块标题 `.group-title`——11.5px / 600 / muted / `letter-spacing: .4px` /
  `flex` 两端布局（单列版的 `.card-title` 与它同源，仅字号 0.5px 之差，勿混用）。
  文件入场可用 `.slot`（虚线圆角、点击/拖放，`filled` 态转实线）替代 `.drop`。

响应式断点：编辑器类 ≤720px 时隐藏次要信息；两栏工具 ≤820px 折单栏。

---

## 四、组件规范（按页面形态选读）

通用部分（header / 卡片 / 按钮 / footer 状态栏）所有页面适用；
其余条款按页面形态选读——没有文件选择的页面跳过 `.drop` 相关条目，
无异步过程的页面跳过 loading 与状态机条目，纯文本页面跳过媒体相关条款。

- **header**：`h1`（16px, 600）+ `.hint`（muted, 12px），border-bottom 分隔。
- **卡片 .card**：`background: var(--panel); border: 1px solid var(--border);
  border-radius: var(--radius)`，内含 `.card-title`——11px / 600 / `letter-spacing: .5px`
  / muted / `flex` 两端布局（左右分开，右侧常放 `.mini` 按钮）；标题是中文，不用 uppercase。
- **按钮**：基础（panel-2 底 / border描边）、`.primary`（accent 底、深绿前字 062315），
  hover 用 accent-2；`.mini` 小号给卡片右上角；禁用态 opacity .45 + not-allowed。
  文字必须反映状态机（如「等待音频…」→「转换中…」→「下载 .caf」），别让禁用按钮
  长期挂着号召性文字。
- **文件选择 .drop**：虚线边框 + hover/dragover 亮 accent；内嵌 `<input type="file">` 隐藏；
  description 里写清支持的类型、体积提示。**整页拖放必须在 dragover/enter/drop 上
  preventDefault**，否则浏览器会导航离开当前页。
- **footer 状态栏**：`.dot`（accent 圆点）+ `#statusText`（muted 12px），所有异步
  过程的状态都落到这里 + 卡片内 `.hint-line`；出错用 `--warn` 色。
- **loading**：CSS 小转圈（border 圆圈旋转，「转换中…」一类文案前放 `<span class="spin">`）。
- **试听/下载混用 blob URL 时**，试听与下载用两条 URL：试听带真实 MIME（音频 `<audio>` 才认），
  下载在桌面端用 `blob.slice(0, size, "application/octet-stream")` 剥 MIME 保住
  `download` 属性的扩展名；移动端（UA 含 Mobi/Android/iPhone/iPad/iPod）浏览器保存时
  一律按 MIME 推导扩展名，只能随它去，文案里提示手动重命名。
- **长度 < 1 秒的媒体**：原生控件会显示 0:00/0:00，附一条「这是取整显示，音频没有问题」
  的说明；自己另给精确时长。

---

## 五、平台能力备忘（按任务涉及的媒体类型选读）

**本节是能力清单，不是功能要求**：只有当前任务的领域真正涉及某类媒体时，
对应条目才生效；纯文本、纯 HTML/CSS 类任务整节跳过，不要向页面里塞无关的探测代码。

- **音频解码**：`AudioContext.decodeAudioData` 是通用解码器（MP3/AAC/WAV/M4A/FLAC/OGG…）。
  注意它会把结果**重采样到 AudioContext 的采样率**——想保源率，先按容器头读出原生采样率
  再 `new AudioContext({ sampleRate: 原率 })`。
- **原生采样率解析**（见 `audio-convert.html`）：MP3 帧头（含 ID3v2 跳过）、WAV `fmt `
  块、MP4 `mdhd` timescale（音频轨通常即采样率）、FLAC STREAMINFO 20-bit、OGG vorbis ident。
- **CAF 封装**：`caff`+`desc`+`data`；**结构字段一律大端**（块大小 BE u64、采样率 BE double），
  仅 PCM 负载按 desc flags 用小端（LE16 → flags 0x2）。对标 `afconvert -f caff -d LEI16`。
- **M4R（iPhone 铃声）**＝AAC@MP4 改后缀。无独立 AAC 编码 API 时走
  `MediaRecorder(stream, { mimeType: "audio/mp4;codecs=mp4a.40.2" })` 实时录制；
  铃声 ≤40 秒；Safari 不支持这条路，Chrome/Edge 可以。能力探测永远用
  `MediaRecorder.isTypeSupported` / `audio.canPlayType` 兜底并给用户说明。
- **图片/视频**：canvas + `drawImage` / `decodeAudioData`↔`MediaRecorder` 同理是黑盒利器；
  canvas 抽帧、GIF 逐帧等见 `video-frame.html`、`image-gif.html`。
- **二进制写入**：用 `DataView`（明确端序）；u64 用 `BigInt`（`setBigUint64`）。
- **已知坑**：①正则字面量结尾的 `\/` 是被转义的斜杠，regex 终止符必须保留一个裸 `/`
  （字面 `/…\*\/` 结尾会令整个文件解析失败）；②`.m4r` 扩展名移动端会被改写（见组件规范
  最后一条）；③清空类操作走 `document.execCommand("delete")` 以便 ⌘Z 可撤销。

---

## 六、需要登记到 index.html 时（index 文件共同提供时）

入口名录由两个数组驱动：

- `GROUPS`：`{ id, name, icon }`，组顺序即 Tab 顺序。现有组：
  图片处理 🖼️ · 视频 🎬 · 音频 🔊 · 文字与字符画 🔤 · 开发工具 🛠️ · 速查手册 📚。
- `TOOLS`：`{ g, name, desc, href, icon }`；`g` 漏写会收进「其它」而不是消失；
  组内顺序按「编辑 → 处理 → 导出」的使用流程摆。

新工具的输出格式、代码块高亮等特殊能力，照惯例在页尾写一段小字说明
（含命令行对照、已知限制），方便人工复核。

---

## 七、交付要求

- 输出**完整的单个 HTML 文件**：从 `<!DOCTYPE html>` 到 `</html>`，
  不给片段、不留「其余代码不变 / 此处省略」的占位。
- 修改现有文件时同样给整份成品（可整体覆盖粘贴使用），另用几句话说明改动点。
- 中文注释只写「为什么」与坑；删净调试 `console.log` 与占位 TODO 后再交付。
- 不确定平台能力时，按第五节结论处理；第五节没覆盖的，写运行时探测兜底
  （如 `isTypeSupported` / `canPlayType`），不要凭记忆断言。
