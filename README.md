# slide-template

用 [Copier](https://copier.readthedocs.io/) 產生一份離線的 [reveal.js](https://revealjs.com/) 簡報。這是範本倉庫（`copier copy` 的來源），不是已經產生好的簡報。

## 安裝 Copier

需要 Python 3.9 以上，以及 Copier 9 以上。任選一種：

```bash
uv tool install 'copier>=9'
```

```bash
pipx install 'copier>=9'
```

```bash
pip install 'copier>=9'
```

範本沒有複製後任務，不用加 `--trust`。

## 建立專案

```bash
copier copy https://github.com/Phate334/slide-template ./my-deck
```

來源也可以是本機的範本目錄。`./my-deck` 必須是還沒存在的目錄，或是空目錄。

會問兩題：簡報標題，以及要載入哪些外掛（空白鍵勾選，Enter 確認）。只有勾選的外掛會複製進專案並寫進 `slide/index.html`；`markdown` 固定載入。預設打開 highlight、notes、math、chalkboard。

不開問答、沿用預設：

```bash
copier copy --defaults https://github.com/Phate334/slide-template ./my-deck
```

## 新增一章

一章是 `slide/` 底下的一個目錄。`rundown.md` 給 AI agent 看這一章要講什麼，不會放映；`slide.md` 才是 reveal.js 的投影片。

建好目錄與這兩個檔之後，編輯 `slide/index.html`，照 `01-welcome` 的 `<section>` 再加一筆。section 的順序就是放映順序：

```html
<section data-markdown="02-topic/slide.md" data-separator="\r?\n---\r?\n" data-separator-vertical="\r?\n--\r?\n" data-separator-notes="^\s*notes?:" data-charset="utf-8"></section>
```

## 預覽

在產生出來的專案根目錄：

```bash
docker compose up --build
```

瀏覽器開 <http://localhost:8000>。
