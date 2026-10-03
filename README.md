# slide-template

用 [Copier](https://copier.readthedocs.io/) 產生一份離線的 [reveal.js](https://revealjs.com/) 簡報。產生出來的 `./slide` 可以用 `python -m http.server` 直接看，不依賴 CDN。

這是**範本倉庫**（`copier copy` 的來源），不是已經產生好的簡報。

## 用 Copier CLI 建立自己的專案

這份目錄是範本。要做自己的簡報，用 Copier 把它複製成一個新目錄，不要直接改範本。

需要 Python 3.9 以上，以及 Copier 9 以上（這份範本在 9.18.2 上驗證過）。任選一種方式安裝 CLI：

```bash
uv tool install 'copier>=9'
```

```bash
pipx install 'copier>=9'
```

```bash
pip install 'copier>=9'
```

範本沒有複製後任務，`slide/vendor/` 是範本裡的靜態檔，所以不用連網，也不用加 `--trust`。

在你想放新專案的地方執行（來源可以是本機路徑，也可以是 git 網址）：

```bash
copier copy /path/to/slide-template ./my-deck
```

CLI 會問三件事。外掛那題是複選（空白鍵勾選，Enter 確認）：

| 問題 | 預設 | 用途 |
| --- | --- | --- |
| `project_name` | `slide-template` | 簡報標題，會出現在投影片、瀏覽器分頁與 README |
| `author` | 空 | 作者，直接按 Enter 留空即可 |
| `plugins` | `markdown`、`highlight`、`notes`、`math`、`chalkboard` | 要寫進 `slide/index.html` 的外掛。`search`、`zoom` 在清單裡但預設不勾。`markdown` 必填 |

`plugins` 的選項與寫進專案的 id：

| 畫面上的選項 | 答案 id | 預設 |
| --- | --- | --- |
| markdown — 官方內建，必要（載入 slide.md） | `markdown` | 開，且不能拿掉 |
| highlight — 官方內建，程式碼高亮 | `highlight` | 開 |
| notes — 官方內建，講者備註 | `notes` | 開 |
| math — 官方內建，KaTeX 數學 | `math` | 開 |
| search — 官方內建，投影片搜尋 | `search` | 關 |
| zoom — 官方內建，Alt 加點擊放大 | `zoom` | 關 |
| chalkboard — 打包的螢光筆額外外掛（非官方內建） | `chalkboard` | 開 |

官方內建是 reveal.js 6.0.1 `dist/plugin/` 裡這份範本附上的那幾個。`chalkboard` 不是官方內建，檔案在 `vendor/chalkboard/plugin.js`。勾了什麼，`slide/index.html` 就只載入什麼（`markdown` 一定有）。這是複製時用 Jinja 寫好的靜態頁，沒有執行期選單，也沒有產生器腳本。

不開問答、沿用預設：

```bash
copier copy --defaults /path/to/slide-template ./my-deck
```

不開問答、自己指定答案：

```bash
copier copy --defaults \
  --data project_name='我的簡報' \
  --data author='你的名字' \
  --data 'plugins=[markdown, highlight, notes, math, chalkboard]' \
  /path/to/slide-template ./my-deck
```

`./my-deck` 必須是還沒存在的目錄，或是空目錄。完成後進入那個目錄，它就是一份獨立專案。

## 產生出來的目錄

```text
my-deck/
├── AGENTS.md          # 寫作規則：rundown.md 給 agent，slide.md 才是投影片
├── Dockerfile         # python:3.12-alpine + http.server
├── compose.yaml       # 對外 8000
├── README.md
└── slide/             # 這個目錄就是整份網站
    ├── index.html     # 放映入口：章節順序與外掛，直接改
    ├── style.css
    ├── 01-welcome/
    │   ├── rundown.md  # 給 AI agent 的本章說明，不會放映
    │   ├── slide.md    # reveal.js 實際投影片
    │   └── diagram.svg
    └── vendor/        # 範本附上的離線檔
```

沒有 `scripts/`，也沒有 `slide/index.md`。

## 新增一章

一章就是 `slide/` 底下的一個目錄，裡面要有兩個檔：

- `rundown.md`：描述這一章，讓 AI agent 了解要參考的資訊、展示流程，或語氣與風格。不會被放映。
- `slide.md`：要編輯或生成、給 reveal.js 展示的實際投影片。

圖和其他靜態檔放在同一章。

```bash
mkdir -p slide/02-topic
```

先寫 `slide/02-topic/rundown.md`，再寫 `slide/02-topic/slide.md`。圖片路徑相對於 `slide/index.html`：

```markdown
# 下一章要講什麼

參考資料、流程或語氣寫在這裡。
```

```markdown
# 下一章

![示意](02-topic/figure.png)

---

## 第二頁

Note:
這段是講者備註。
```

第一段是 `rundown.md`，第二段是 `slide.md`。

然後直接編輯 `slide/index.html`，照 `01-welcome` 的 section 再加一筆。section 的順序就是放映順序：

```html
<section data-markdown="02-topic/slide.md" data-separator="\r?\n---\r?\n" data-separator-vertical="\r?\n--\r?\n" data-separator-notes="^\s*notes?:" data-charset="utf-8"></section>
```

水平換頁用單獨一行 `---`，同一章的直向子頁用單獨一行 `--`，備註用行首 `Note:`。

除非必要，`slide.md` 只用標準語意化 Markdown。外觀改 `slide/style.css`，不要在內容裡堆 class。`rundown.md` 與 `slide.md` 的差別寫在產生專案的 `AGENTS.md`。

## 本地預覽

在 `./slide` 開伺服器（外部 Markdown 不能用 `file://` 打開）：

```bash
python -m http.server 8000 --directory slide
```

或在專案根目錄用容器。映像是 `python:3.12-alpine`，只跑標準庫的 `http.server`，把 `./slide` 掛在 8000：

```bash
docker compose up --build
```

瀏覽器開 <http://localhost:8000>。

## 離線 vendor

`slide/index.html` 只引用本機路徑。檔案在範本的 `template/slide/vendor/`，複製時原樣帶進專案，沒有下載腳本。版本寫在產生專案的 `slide/vendor/VERSIONS.txt`。2026-10-03 核對過的版本：

| 套件 | 版本 | 放到 |
| --- | --- | --- |
| [hakimel/reveal.js](https://github.com/hakimel/reveal.js/releases/tag/6.0.1) | 6.0.1 | `slide/vendor/reveal.js/dist/` |
| [KaTeX](https://github.com/KaTeX/KaTeX) | 0.19.0 | `slide/vendor/katex/dist/` |
| [rajgoel/reveal.js-plugins](https://github.com/rajgoel/reveal.js-plugins/releases/tag/4.6.0) 的 chalkboard | 4.6.0（plugin.js 標頭為 2.3.3） | `slide/vendor/chalkboard/` |

建立專案時可勾的 id（預設會寫進 `index.html` 的是 markdown、highlight、notes、math、chalkboard；search 與 zoom 要另外勾，或事後自己改 HTML）：

| id | 全域物件 | 本機腳本 |
| --- | --- | --- |
| `markdown` | `RevealMarkdown` | `vendor/reveal.js/dist/plugin/markdown.js` |
| `highlight` | `RevealHighlight` | `vendor/reveal.js/dist/plugin/highlight.js`，樣式 `vendor/reveal.js/dist/plugin/highlight/monokai.css` |
| `notes` | `RevealNotes` | `vendor/reveal.js/dist/plugin/notes.js` |
| `math` | `RevealMath.KaTeX` | `vendor/reveal.js/dist/plugin/math.js`，並設定 `katex.local = vendor/katex` |
| `search` | `RevealSearch` | `vendor/reveal.js/dist/plugin/search.js` |
| `zoom` | `RevealZoom` | `vendor/reveal.js/dist/plugin/zoom.js` |
| `chalkboard` | `RevealChalkboard` | `vendor/chalkboard/plugin.js` 與 `vendor/chalkboard/style.css`（打包的螢光筆額外外掛，不是官方內建） |

`math` 用的是 KaTeX，不是會向 CDN 要檔案的 MathJax。reveal.js 6 的數學外掛在沒有 `katex.local` 時會去 jsDelivr；勾了 `math` 時，`index.html` 會帶上 `local: "vendor/katex"`，所以初始化不會走那條路。

螢光筆是 chalkboard 的 notes canvas：放映按 `C` 在投影片上畫，按 `B` 開黑板。內容裡的重點用 `<mark>`，顏色由 `style.css` 決定。chalkboard 4.6 的按鈕圖示要另外的 Font Awesome 與 customcontrols，那兩個官方說明是用 CDN，所以這份範本不載入它們，改用鍵盤。

`slide/vendor/` 不要手改。

## index.html 怎麼排章節與外掛

複製時 Jinja 只把勾選的外掛寫進 `slide/index.html`：

- `<link>` 與 `<script>` 的順序是 markdown、highlight、notes、math、search、zoom、chalkboard，沒勾的不會出現。必須包含 `markdown`。
- `Reveal.initialize({ plugins })` 用同一份清單。勾了 `math` 才會有 `katex: { local: "vendor/katex" }`。
- 預設章節只有 `01-welcome`，對應一個 `<section data-markdown="01-welcome/slide.md" ...>`。之後加章就再加 section。
- `rundown.md` 給 agent 看，不會被放映。

第一個出現在標題的是建立專案時的 `project_name`。有填作者時會多一行 author meta。

## 授權

範本本身可以隨專案使用。reveal.js、KaTeX、chalkboard 各有自己的 MIT 授權，放在 `slide/vendor/`。
