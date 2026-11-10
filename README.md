# TXT → EPUB

A small web app that turns plain `.txt` files into proper EPUB books, right on your iPhone. You can add it to your Home Screen, and after that it works offline. Nothing gets uploaded: your files are read and converted on the phone.

- Converts one file or a whole batch
- Makes valid EPUB 3 files (with an EPUB 2 contents file for older readers) that open in Apple Books, Kobo, Kindle (via Send to Kindle), Google Play Books, and others
- Names each EPUB after the book's detected title
- Finds chapters on its own, or splits at your own marker like `===CHAPTER===`
- Covers: matched by file name, the same cover for every book, an auto-made text cover, or no cover
- Can merge chapter-per-file downloads into one book
- Shows a preview of the title and chapters before you convert
- Remembers your settings

---

## 1. Getting it onto your iPhone

There are two ways. Here's how they compare:

| Claude-hosted link | GitHub Pages (recommended) |
|---|---|
| Works right now, no setup | One-time setup, about 5 minutes, all in Safari |
| Needs internet | **Works offline** after the first open |
| Each download asks you to confirm | Saves straight to **Books** via the share sheet |
| Can't be installed as a real app | **Add to Home Screen** and it acts like an app |

### Option A: GitHub Pages (offline home-screen app)

You only need Safari and a free GitHub account. No computer.

1. **Unzip the files.** Save `txt2epub-site.zip` to the Files app and tap it. You'll get a folder with `index.html`, `sw.js`, `manifest.webmanifest`, three `icon-*.png` files, and this README.
2. **Make a repository.** Go to **github.com** in Safari and sign in (or sign up). Tap **+** → **New repository**. Name it `txt2epub`, leave it **Public**, tick **Add a README file**, then tap **Create repository**.
   - If GitHub's mobile layout hides a button, tap **aA** in the address bar → **Request Desktop Website**.
3. **Upload.** In the new repository, tap **Add file** → **Upload files** → **choose your files**. Tap **Browse**, open the unzipped folder, tap **Select**, select all the files, and tap **Open**. Scroll down and tap **Commit changes**.
4. **Turn on Pages.** Tap **Settings** → **Pages** (in the left menu). Under **Build and deployment**, set **Source** to **Deploy from a branch**, then **Branch** to `main` with `/ (root)`. Tap **Save**.
5. **Wait about a minute** and refresh. Pages shows your link, which looks like `https://YOUR-NAME.github.io/txt2epub/`.
6. **Install it.** Open that link in **Safari**, tap the **Share** button, then **Add to Home Screen** → **Add**.
7. **Open it once from the new icon while you're online.** The footer will say **"✓ Ready to work offline."** From then on it works in airplane mode.

**Updating later:** upload the new `index.html` and `sw.js` over the old ones and commit. The app picks up the new version the next time you open it (sometimes it takes two launches).

### Option B: Claude-hosted link

Open the link in Safari or the Claude app. It runs the same code, with these limits: it needs a connection, it can't be installed, and each download shows a confirmation first.

---

## 2. Using it

1. Tap **Choose TXT files** and pick one or more files from the Files app (iCloud Drive, On My iPhone, Downloads…).
2. Each file gets a card with the detected **title**, chapter count, word count, and the **file name it will save as**. Tap the title to edit it.
3. Tap **Details & chapters** to check the author, language, description, and encoding, and to see the chapter list. Tap any chapter to peek at its first lines.
4. Optional: open **Covers**, **Details & style**, or **Text options** to change how things are done.
5. Tap **Convert**.
6. Tap **Open in Books** on a book (or **Share all** for a batch) and pick **Books** in the share sheet. **Save to Files** and **ZIP** save copies to the Files app instead.

**Chapter-per-file downloads** (Chapter 1.txt, Chapter 2.txt, …): add them all and switch to **One combined book**. The files are sorted naturally (Ch 2 before Ch 10), and you can reorder them with the arrows.

---

## 3. How automatic renaming works

Each book's title comes from the first of these that it finds:

1. **Project Gutenberg info.** The `Title:` and `Author:` lines in a Gutenberg header.
2. **A story-info block.** At least two `Key: Value` lines near the top, such as `Title:`, `Author:`, `Summary:`, `Rating:`. This is the style FanFicFare and many fanfic or web-novel tools use.
3. **The first line**, if it looks like a title: short, Title Case or ALL CAPS, no full stop at the end, followed by a blank line or a `by Author` line.
4. **A chapter heading**, when the file is a single chapter. If the file also has a story title, you get both: `Ashfall Chronicles – Chapter 12: The Return`. That keeps chapter files from all getting the same name.
5. **The TXT file name**, as a last resort (with underscores turned into spaces).

The card says which one it used (for example "title from first line").

Then the title is made safe to use as a file name:

- `/ \ : * ? " < > |` and invisible characters are removed (they break on iOS, macOS, Windows, or Android). A colon becomes ` - `, so `Chapter 3: Who's There?` → `Chapter 3 - Who's There.epub`.
- Long titles are trimmed to 80 characters at a word break.
- Reserved Windows names like `CON` get an underscore.
- If two books in the same batch end up with the same name, the later ones become `Title (2).epub`, `Title (3).epub`.

When you edit a title, the "Saves as" name updates as you type.

---

## 4. How cover assignment works

Open **Covers** and pick a mode:

| Mode | What happens |
|---|---|
| **Match names** (default) | Each book gets the image whose name matches its file name or title |
| **Same for all** | Every book gets one image (tap an image in the tray to choose it) |
| **Text cover** | Each book gets a simple cover with its title and author |
| **None** | No cover |

**How name matching works:**

- Case, accents, punctuation, and words like *cover*, *front*, *art*, *image* are ignored. So `My Story - Cover.JPG` matches `my_story.txt`.
- An exact match on the file name or the title wins first. After that, the longest partial match wins (e.g. `Ashfall.jpg` → `Ashfall Chronicles - Ch 01.txt`).
- Numbers have to match whole, so `Book 1.jpg` never lands on `Book 10.txt`.
- One book plus one image means that image is used, whatever its name.
- Matched images show `→ Book Title` in green under the thumbnail.
- **If no image matches**, the book gets a text cover (or none; you can choose).

**Per-book override:** tap any book's cover to pick an image from the tray, a text cover, no cover, or a new image. That choice sticks even if you change the mode.

**Image handling:** JPG, PNG, WebP, and HEIC (when Safari can read it) all work. Every cover is resized to fit within 1600 × 2560 px and saved as a JPEG, the one format every reader supports. Transparent PNGs get a white background. Text covers are 1600 × 2400.

---

## 5. Text options

| Option | What it does |
|---|---|
| **Chapters: Detect automatically** | Finds `Chapter 12`, `Chapter Twelve: Title`, `CHAPTER XII.`, `Part One`, `Book II`, `Prologue`/`Epilogue`/`Interlude`, `第一章`, Markdown `#` headings, numbered `1. Title` headings, lone Roman numerals, and ALL-CAPS heading lines. Parts nest their chapters in the contents. A printed "Contents" list that repeats the real headings is removed. |
| **Chapter marker** | Any line that starts with your marker becomes a chapter, even in Detect mode. Text after the marker (or a short line right under it) becomes the chapter title. For a pattern, wrap it in slashes, e.g. `/^#{1,2} /`. |
| **Paragraphs** | Auto handles blank-line paragraphs, one-paragraph-per-line text, hard-wrapped Gutenberg lines (rejoined), and indented paragraphs. Poems and letters keep their line breaks. |
| **Whitespace** | *Tidy up* collapses stray spaces. *Keep exactly* preserves every line break and space. |
| **Smart quotes / Dashes** | `"x"` → “x”, `it's` → it’s, `'90s` → ’90s, `--` → —, `...` → … |
| **Fix garbled characters** | Repairs `â€™`-style text that was saved with the wrong encoding. |
| **Asterisks → italics** | `*word*` and `_word_` → italic, `**word**` → bold. Off by default. |
| **Remove author's notes** | Drops paragraphs that start with `A/N`, `AN:`, or `Author's note`, plus AO3 "Notes:" labels and the paragraph right after them. Off by default. |
| **Strip Project Gutenberg license** | Uses the header's title and author, then cuts the header, the license, and the "Produced by" credits. |
| **Text encoding** | Auto checks for a byte-order mark, then UTF-16, then UTF-8, then guesses between Western, Cyrillic, Chinese, Japanese, and Korean encodings. Change it here for everything, or per book under Details. |

Scene breaks like `***`, `* * *`, `~~~`, `oOo`, `xXx`, and `-x-` become a centred `*   *   *`.

**Reading styles:** Comfortable (indented), Spaced (web-style, good for dialogue-heavy fanfic), Compact, Large print, and Classic serif (Georgia/Palatino, justified). In Apple Books, Classic serif shows its own font when the font setting is **Original**.

---

## 6. iOS quirks this works around

1. **Opening an HTML file from Files doesn't work.** iOS shows it in a preview that doesn't run JavaScript. That's why the app needs to be hosted: GitHub Pages (free, HTTPS) or the Claude link.
2. **Offline needs a service worker, and that needs HTTPS.** GitHub Pages provides it. The worker caches the app on first open and refreshes itself in the background.
3. **Home-screen apps keep their own storage.** Settings saved in a Safari tab don't carry over to the Home Screen app, and vice versa. On the plus side, home-screen apps are exempt from Safari's 7-day storage cleanup.
4. **The share sheet only opens straight from a tap.** If the code does slow work between your tap and the share call, iOS refuses it. So EPUBs are built when you tap **Convert**, and **Open in Books** hands over a finished file instantly.
5. **Safari blocks several downloads at once.** Batches go out as one ZIP, or through **Share all**.
6. **Blob downloads need time.** Download links stay alive for 60 seconds so the save sheet can finish. Saved files land in **Files → Downloads**.
7. **Canvas limits.** iOS caps a canvas at about 16.7 megapixels and limits total canvas memory. Big photos (48 MP iPhone shots) are scaled down in steps, and every canvas is released right after use, so covers don't silently fail halfway through a batch.
8. **HEIC photos.** The Photos picker converts them to JPEG. Covers are always re-saved as JPEG anyway.
9. **Zoom on tap.** iOS zooms into any text box with text under 16px, so all inputs use 16px.
10. **The keyboard shoves fixed bars around.** The bottom Convert bar hides while you're typing.
11. **The notch and home indicator.** The layout uses safe-area padding, so nothing hides under them.
12. **The file picker greys out "wrong" files.** It accepts `.txt`, `.text`, `.md`, and plain text. Rename other text files to `.txt`.
13. **Native compression needs iOS 16.4+.** On older iOS, EPUBs are saved uncompressed. They're bigger but just as valid.
14. **The Claude-hosted page runs in a sandbox** that blocks downloads, sharing, and service workers. There, the app saves through Claude's own download prompt instead.

---

## 7. Troubleshooting

- **Odd symbols (Ã©, â€™, ���):** turn on *Fix garbled characters*, or open the book's details and try another encoding (Western 1252 for old English files, GB18030 for Chinese, Shift_JIS for Japanese).
- **Everything is one long chapter:** your headings may use a style the detector doesn't know. Put a marker like `===CHAPTER===` before each chapter, or set a pattern marker.
- **Too many chapters (random lines became headings):** set *Chapters* to *Only split at my marker* or *No chapters*.
- **Paragraphs glued together or split mid-sentence:** change *Paragraphs* from Auto to the option that matches your file.
- **"This doesn't look like plain text":** the file is probably a PDF, Word, RTF, or EPUB with a `.txt` name. Export it as plain text first.
- **Apple Books didn't show up in the share sheet:** scroll the app row and tap **More**, or use **Save to Files**, then open the file in Files → Share → Books.

---

## 8. For tinkerers: how the code is laid out

The source is split into small modules. `build.py` inlines them into a single `index.html`, so there's only one page to host.

| File | Job |
|---|---|
| `src/js/zip.js` | Tiny ZIP writer (no libraries). `mimetype` is stored first, uncompressed, as EPUB requires. Uses native `CompressionStream` when there is one. |
| `src/js/encoding.js` | Bytes → text: BOM / UTF-16 / UTF-8 checks, legacy-encoding scoring, mojibake repair |
| `src/js/typography.js` | Smart quotes, dashes, ellipses, emphasis markers, taming ALL-CAPS headings |
| `src/js/parser.js` | Text → book: Gutenberg/header metadata, title detection, chapter detection, paragraph layout, scene breaks, combining files |
| `src/js/names.js` | Safe, unique file names |
| `src/js/epub.js` | Book → EPUB 3 (+ NCX): OPF, nav, cover page, title page, style presets |
| `src/js/covers.js` | Cover resizing, text covers, matching by name |
| `src/js/platform.js` | Settings storage, share/download, service worker, Claude-host detection |
| `src/js/app.js` | The interface |
| `src/sw.js`, `src/manifest.webmanifest` | Offline support and Home Screen install |

Build: `python3 build.py` → `dist/`. Tests: `node test/run-node.js` (parses the samples and builds EPUBs), `node test/edge-test.js`, and `test/ui-test.js` (Playwright WebKit at iPhone size). Every generated EPUB passes **epubcheck 5.3** with zero errors and zero warnings.
