# 簡報寫作規則

這份專案是一份 reveal.js 簡報。可以對外提供的檔案都在 `./slide`。預覽請在專案根目錄執行 `docker compose up --build`，瀏覽器開 http://localhost:8000。

## 必須遵守

除非必要，所有投影片內容都使用標準語意化 Markdown，外觀只由簡報根目錄的 `slide/style.css` 負責。

這條規則的意思是：

- 用標題、段落、清單、連結、圖片、引用、表格、`<mark>`、`<kbd>` 這些本來就有意義的寫法。
- 不要為了排版發明 class、不要把 inline style 寫進 `slide.md`、不要把 reveal.js 的版面 class 當內容格式。
- 要改顏色、字級、螢光筆、表格或引用的樣子，改 `slide/style.css`，不要改每一章的 Markdown。
- 程式碼用 fenced code block（語法高亮交給 highlight 外掛）。講者備註用行首 `Note:`。數學用 `$...$` 與 `$$...$$`。

只有 reveal.js 或某個外掛沒有對應的語意寫法時，才可以例外，並在該章用一句話說明為什麼。

## 一章一個目錄

每一章是 `slide/<章節>/`。這一章有兩個 Markdown，用途不同：

- `rundown.md` 用來描述這一章，讓 AI agent 了解這段簡報在講什麼。可以寫要參考的資訊、簡報展示流程，或語氣與風格。它不會被 reveal.js 放映。
- `slide.md` 才是要編輯或產生、給 reveal.js 展示的實際投影片內容。`index.html` 只載入這個檔。

除非必要，`slide.md` 只用標準語意化 Markdown，外觀只改簡報根目錄的 `slide/style.css`（見上面「必須遵守」）。

另外：

- 這一章的圖和其他靜態檔放在同一個目錄。
- 外部 Markdown 的網址是瀏覽器依 `slide/index.html` 去解析的，所以圖要寫成 `章節/檔名.png`，不是只寫檔名。
- 章節順序不看資料夾排序，只看 `slide/index.html` 裡 `<section>` 的順序。

## 放映入口

`slide/index.html` 決定兩件事：章節顯示順序，以及要載入哪些外掛。它就是放映頁，請直接改這個檔。沒有 `slide/index.md`，也沒有產生器腳本。

- 要加一章：建好 `slide/<章節>/slide.md`（與給 agent 的 `rundown.md`）後，照現有的 `01-welcome` section 再加一筆 `data-markdown`。
- 外掛的 `<script>` 與 `Reveal.initialize({ plugins })` 也在這個檔。建立專案時只寫入勾選的外掛（`markdown` 一定有）。沒有執行期選單。
- 不要另外做一份清單檔再去產生這個 HTML。

## 放映

- 水平換頁：單獨一行 `---`
- 同一章的直向子頁：單獨一行 `--`
- 講者備註：單獨一行 `Note:`，這一行之後到下一張投影片之前都是備註
- 放映時按 `S` 開備註視窗（需要 notes 外掛）
- 若載入了 chalkboard（打包的螢光筆額外外掛，不是官方內建）：按 `C` 在投影片上畫，按 `B` 開黑板
