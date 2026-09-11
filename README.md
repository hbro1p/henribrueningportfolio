# Henri Brüning — Portfolio

Static site. No build step, no dependencies. `index.html` is the whole app;
photos and videos live next to it and are listed in `media.json`.

---

## 1. Put it online (once)

1. Create a new repository on GitHub, e.g. `portfolio`.
2. Upload everything in this folder into it (`index.html`, `media.json`,
   `.nojekyll`, the `img/` and `video/` folders).
3. Repository → **Settings → Pages** → *Source: Deploy from a branch* →
   branch `main`, folder `/ (root)` → **Save**.
4. A minute later the site is at `https://<your-name>.github.io/portfolio/`.

Own domain: Settings → Pages → *Custom domain*, then point a CNAME record at
`<your-name>.github.io` in your domain provider.

---

## 2. Add photos

Put the files in `img/` and list them in `media.json`.
`media.example.json` next to it shows the full shape — copy from there.

```json
{
  "photos": [
    { "file": "img/photo-01.jpg", "caption": "SAHARA", "where": "Egypt · October 2025" },
    { "file": "img/photo-02.jpg", "caption": "NIGHT BUS", "where": "Paris · January 2025" }
  ],
  "videos": []
}
```

* `file` — the path, exactly as the file is named. Lower case, no spaces, no umlauts.
* `caption` — the line on the photo.
* `where` — the small grey line inside the opened window. Optional.
* `text` — a longer paragraph inside the window. Optional.

As soon as `photos` is not empty, the "COMING SOON" card disappears and your
photos float in the PHOTOS folder instead.

### Compress first — this matters

A 20 MB camera file does not belong on a website. Resized it looks identical
on screen and is 40× smaller. On a Mac, in the folder with your originals:

```bash
# needs ImageMagick:  brew install imagemagick
mkdir -p out
for f in *.JPG *.jpg; do
  magick "$f" -auto-orient -resize 2400x2400\> -quality 82 -strip "out/${f%.*}.jpg"
done
```

Target: **under 600 KB per photo.** Upload what lands in `out/`.

---

## 3. Add videos

Put the files in `video/` and list them:

```json
{
  "photos": [],
  "videos": [
    { "file": "video/reel-01.mp4", "poster": "video/reel-01.jpg",
      "title": "Kelwin · new build", "who": "KELWIN LAMPARTER" }
  ]
}
```

* `file` — the mp4. **H.264 + AAC**, otherwise Safari and iPhone will not play it.
* `poster` — a still frame shown before playback. Optional but nicer.
* `title`, `who` — shown in the player header.

They then play directly in the floating television, with sound and controls,
with no Instagram involved.

### Compress first

```bash
# needs ffmpeg:  brew install ffmpeg
ffmpeg -i input.mov -vf "scale=1080:-2" -c:v libx264 -crf 24 -preset slow \
       -profile:v high -pix_fmt yuv420p -movflags +faststart \
       -c:a aac -b:a 128k reel-01.mp4

# one poster frame, from the first second
ffmpeg -i reel-01.mp4 -ss 0.5 -frames:v 1 -q:v 4 reel-01.jpg
```

Target: **under 10 MB per video.** `-movflags +faststart` is what lets it start
playing before the whole file has downloaded — do not leave it out.

---

## 4. Limits worth knowing

| | GitHub Pages |
|---|---|
| whole site | 1 GB |
| single file | 100 MB |
| traffic | 100 GB per month |

Compressed, 12 photos and 12 videos come to roughly 100 MB. Plenty of room.

---

## 5. If something does not show up

* **Photos missing** — open `https://<your-site>/media.json` in the browser.
  If it 404s the file is in the wrong place; if it shows an error, the JSON has
  a typo (a trailing comma after the last entry is the usual one).
* **A photo is a broken box** — the name in `media.json` does not match the real
  file name. It is case sensitive: `Photo-01.JPG` is not `photo-01.jpg`.
* **A video will not play on iPhone** — it is not H.264/AAC. Re-encode with the
  ffmpeg line above.
* **Nothing changed after uploading** — GitHub Pages caches; wait a minute and
  reload with Shift held down.
