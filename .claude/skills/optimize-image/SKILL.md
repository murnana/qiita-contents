---
name: optimize-image
description: Shrink and optimize images for Qiita articles. Builds candidates in several formats from an image in assets/{article-slug}/sources/, lets a human compare sizes and pick one, then places the choice in images/. Use when asked to "make this image smaller", "optimize the image", or "put it in images/".
argument-hint: <path to image in sources> [width in px (default 800)]
---

# Image Optimization

Build candidates in formats that Qiita accepts from a source image in `assets/{article-slug}/sources/`.
The best format depends on the image, so a human compares the candidates and chooses.

## Qiita specs (source: https://help.qiita.com/ja/articles/qiita-image-upload)

- Supported formats: `avif` / `gif` / `jpeg` / `png` / `tiff`. **WebP is not supported** (upload was confirmed to fail).
- Up to 10MB per file and 100MB per month in total.
- No resolution limit, but articles reach their maximum content width at about 800px.
- The specs may change. If in doubt, re-read the help article.

## Steps

1. Determine the input image and the article slug. If the path is not `assets/{slug}/sources/{name}.png`, ask the user.
2. Decide the width. Default is 800px. Never upscale: if the source is narrower, keep its width.
3. Write the following candidates to `assets/{slug}/candidates/` with `ffmpeg`.

   ```bash
   S="scale='min(800,iw)':-2:flags=lanczos"   # replace 800 with the chosen width
   ffmpeg -y -i "$IN" -vf "$S,split[a][b];[a]palettegen[p];[b][p]paletteuse=dither=sierra2_4a" "$OUT/$name-256.png"
   ffmpeg -y -i "$IN" -vf "$S" -compression_level 100 "$OUT/$name-full.png"
   ffmpeg -y -i "$IN" -vf "$S" -q:v 3 "$OUT/$name.jpg"
   ffmpeg -y -i "$IN" -vf "$S" -c:v libaom-av1 -crf 20 -pix_fmt yuv444p -still-picture 1 "$OUT/$name-crf20.avif"
   ffmpeg -y -i "$IN" -vf "$S" -c:v libaom-av1 -crf 30 -pix_fmt yuv444p -still-picture 1 "$OUT/$name-crf30.avif"
   ```

   - Skip GIF and TIFF for still images: they were no smaller than PNG in testing. If the source is an animated GIF, handle it separately and discuss shrinking options (e.g. `gifsicle` or ffmpeg) with the user.
   - Use `yuv444p` for AVIF to avoid blurring text and to keep browser compatibility.

4. Show a table of each candidate's format, size, and whether it is within 10MB. Open the images with `Read` and add observations such as blurred text or banding from color reduction.
5. **Wait for the human to pick one.** Do not choose on their behalf.
6. Move the chosen file to `assets/{slug}/images/`, keeping the source's base name and changing only the extension (e.g. `welcome-open-256.png` becomes `welcome-open.png`). Delete `candidates/`.
7. Point out the next steps:
   - Upload at https://qiita.com/settings/images (mind the 100MB monthly limit).
   - Record the upload in `docs/development/asset-management/logs/{slug}.md`.
   - Reference the Qiita URL in the article with Japanese alt text.

## Notes

- Never modify the originals in `sources/`.
- Keep `images/` and `candidates/` separate. `images/` holds only the chosen file because it is tracked in Git.
- AVIF is on Qiita's supported list and is usually the smallest. If the user picks AVIF, tell them to check the rendering after upload.
