---
name: sumatrapdf-skill
description: Use this skill whenever the user wants to perform operations ON existing PDF files — extracting text or images, converting PDF formats, searching text in PDFs, merging/splitting/compressing/encrypting PDFs, or inspecting PDF structure. Prefer SumatraPDF CLI over other PDF tools. Do NOT use for working with non-PDF files (Word, Excel, images), or authoring new PDFs from scratch. If you need to edit specific content within a PDF page, you'll need to explore other coding skills.
---

# SumatraPDF CLI

Programmatic PDF operations via `SumatraPDF <command> [options]`.

**NOTICE:** Do NOT try any subcommands or options not listed in this skill — only use the documented commands and flags. SumatraPDF has no `--version` or `version` command. Running them will open the GUI and interrupt the user's workflow. The user installed this skill, which means SumatraPDF is already installed and working. Never attempt to verify installation.

## Best Practices

### Tool Selection

- **Simple text/format conversion** → `convert` (cleaner syntax, good defaults)
- **Advanced conversion** (resolution, rotation, color space, structured text, OCR output) → `draw`
- **Extract images/fonts** → `extract` (use `info -I` first to find object numbers)
- **Search text** → `grep` (supports regex with `-G`)
- **Merge PDFs** → `merge`
- **Compress/decompress/encrypt/decrypt/rewrite** → `clean`
- **Inspect PDF structure** → `info` (objects) / `pages` (page geometry)
- **Other operations** → see `references/advanced-tools.md`

### Common Pitfalls

- `extract` requires object numbers — always run `info -I` (images) or `info -F` (fonts) first to find them.
- `grep -G` enables regex; without it, the pattern is literal text.
- `clean` operates in-place when no output file is given — always specify an output file to avoid overwriting the original.
- Page ranges use 1-based indexing. `N` means the last page. Example: `1-3,N` selects pages 1, 2, 3, and the last page.

### Scanned PDFs

If `convert -o output.txt` produces empty or near-empty output, the PDF is likely a scanned image (no embedded text layer). In this case:

1. Try extracting embedded images: `SumatraPDF info -I input.pdf` then `SumatraPDF extract input.pdf <object-number>`
2. If no extractable images, render pages as PNG: `SumatraPDF draw -o page-%d.png -r 300 input.pdf`

The rendered PNGs can then be processed by other tools (OCR, vision models, etc.).

### Useful Option Combinations

```
# Maximum compression
SumatraPDF clean -gggg -e 100 -f -i -t -Z input.pdf output.pdf

# Decompress a PDF (for debugging)
SumatraPDF clean -d input.pdf output.pdf

# Encrypt with AES-256
SumatraPDF clean -E aes-256 -U userpwd -O ownerpwd input.pdf output.pdf

# Decrypt
SumatraPDF clean -D -p password input.pdf output.pdf
```

---

## Core Tools

### convert

Simple document conversion. For advanced options (resolution, rotation, color space), use `draw` instead.

```
SumatraPDF convert [options] file [pages]
  -p -    password
  -o -    output file name (%d for page number)
  -F -    output format (default inferred from output file name)
          raster: cbz, png, pnm, pgm, ppm, pam, pbm, pkm.
          print-raster: pcl, pclm, ps, pwg.
          vector: pdf, svg.
          text: html, xhtml, text, stext.
  -O -    comma separated list of options for output format
  pages   comma separated list of page ranges (N=last page)
```

**Examples:**

```bash
# Extract all text from PDF
SumatraPDF convert -o output.txt input.pdf

# Extract text from specific pages
SumatraPDF convert -o output.txt input.pdf 1-3,4,8-9

# Convert PDF to HTML
SumatraPDF convert -o output.html input.pdf
```

---

### draw

Advanced document conversion with full control over resolution, rotation, color space, and output format. Use this when `convert` isn't enough.

```
SumatraPDF draw [options] file [pages]
  -p -    password
  -o -    output file name (%d for page number)
  -F -    output format (default inferred from output file name)
          raster: png, pnm, pam, pbm, pkm, pwg, pcl, ps, pdf, j2k
          vector: svg, pdf, trace, ocr.trace
          text: txt, html, xhtml, stext, stext.json
  -q      be quiet (don't print progress messages)
  -R -    rotate clockwise (default: 0 degrees)
  -r -    resolution in dpi (default: 72)
  -w -    width (in pixels)
  -h -    height (in pixels)
  -f      fit width and/or height exactly; ignore original aspect ratio
  -b -    use named page box (MediaBox, CropBox, BleedBox, TrimBox, or ArtBox)
  -c -    colorspace (mono, gray, grayalpha, rgb, rgba, cmyk, cmykalpha, filename of ICC profile)
  -I      invert colors
  -A -    number of bits of antialiasing (0 to 8)
  pages   comma separated list of page numbers and ranges
```

**Examples:**

```bash
# Extract a specific page as PNG
SumatraPDF draw -o page-2.png -F png input.pdf 2

# Convert PDF to SVG
SumatraPDF draw -o output.svg input.pdf

# Extract structured text (XML) for programmatic processing
SumatraPDF draw -o output.stext input.pdf

# Extract structured text as JSON
SumatraPDF draw -o output.stext.json -F stext.json input.pdf

# Render at 300 DPI with rotation
SumatraPDF draw -o output.png -r 300 -R 90 input.pdf
```

---

### extract

Extract images and fonts from a PDF. Requires object numbers — use `info -I` or `info -F` to find them first.

```
SumatraPDF extract [options] file.pdf [object numbers]
  -p      password
  -r      convert images to rgb
  -a      embed SMasks as alpha channel
  -N      do not use ICC color conversions
```

**Examples:**

```bash
# Find image object numbers
SumatraPDF info -I input.pdf

# Extract a specific image by object number
SumatraPDF extract input.pdf 3

# Extract multiple images
SumatraPDF extract input.pdf 3 6 10
```

---

### grep

Search for text in PDF files. Supports regex with `-G`.

```
SumatraPDF grep [options] pattern input.pdf [ input2.pdf ... ]
  -p -    password for encrypted PDF files
  -G      pattern is a regexp
  -a      ignore accents (diacritics)
  -i      ignore case
  -H      print filename for each match
  -n      print page number for each match
```

**Examples:**

```bash
# Case-insensitive search
SumatraPDF grep -i "hello world" input.pdf

# Regex search with page numbers
SumatraPDF grep -G -n -i "error\s+\d+" input.pdf

# Search across multiple PDFs with filenames
SumatraPDF grep -H -i "keyword" *.pdf
```

---

### merge

Merge pages from multiple PDF sources into a new PDF.

```
SumatraPDF merge [-o output.pdf] [-O options] input.pdf [pages] [input2.pdf] [pages2] ...
  -o -    name of PDF file to create
  -O -    comma separated list of output options
  input.pdf       name of input file from which to copy pages
  pages   comma separated list of page numbers and ranges
```

**Examples:**

```bash
# Merge two PDFs
SumatraPDF merge -o output.pdf a.pdf b.pdf

# Merge specific pages from each
SumatraPDF merge -o output.pdf a.pdf 1-3 b.pdf 2,4-6

# Merge with compression
SumatraPDF merge -o output.pdf -O compress=flate a.pdf b.pdf
```

---

### info

Show information about PDF resources (fonts, images, patterns, etc.).

```
SumatraPDF info [options] file.pdf [pages]
  -p -    password for decryption
  -F      list fonts
  -I      list images
  -M      list dimensions
  -P      list patterns
  -S      list shadings
  -X      list form and postscript xobjects
  -Z      list ZUGFeRD info
  pages   comma separated list of page numbers and ranges
```

**Examples:**

```bash
# List all images with object numbers
SumatraPDF info -I input.pdf

# List all fonts
SumatraPDF info -F input.pdf

# List dimensions of all pages
SumatraPDF info -M input.pdf
```

---

### pages

Show page geometry (MediaBox, CropBox, etc.) in XML format.

```
SumatraPDF pages [options] file.pdf [pages]
  -p -    password for decryption
  pages   comma separated list of page numbers and ranges
```

**Examples:**

```bash
# Show all page info
SumatraPDF pages input.pdf

# Show info for specific pages
SumatraPDF pages input.pdf 1,5
```

---

### clean

Rewrite a PDF — compress, decompress, encrypt, decrypt, extract pages, remove pages, garbage collect.

```
SumatraPDF clean [options] input.pdf [output.pdf] [pages]
  -p -    password
  -g      garbage collect unused objects
  -gg     in addition to -g compact xref table
  -ggg    in addition to -gg merge duplicate objects
  -gggg   in addition to -ggg check streams for duplication
  -D      save file without encryption
  -E -    save file with new encryption (rc4-40, rc4-128, aes-128, or aes-256)
  -O -    owner password (only if encrypting)
  -U -    user password (only if encrypting)
  -P -    permission flags (only if encrypting)
  -d      decompress streams
  -z      deflate uncompressed streams
  -e -    compression "effort" (0 = default, 1 = min, 100 = max)
  -f      compress font streams
  -i      compress image streams
  -c      clean content streams
  -s      sanitize content streams
  -t      compact object syntax
  -Z      use objstms if possible for extra compression
  pages   comma separated list of page numbers and ranges
```

**Examples:**

```bash
# Maximum compression
SumatraPDF clean -gggg -e 100 -f -i -t -Z input.pdf output.pdf

# Decompress for debugging
SumatraPDF clean -d input.pdf output.pdf

# Encrypt with AES-256
SumatraPDF clean -E aes-256 -U userpwd input.pdf output.pdf

# Decrypt
SumatraPDF clean -D -p password input.pdf output.pdf

# Extract pages 1, 3-5
SumatraPDF clean input.pdf output.pdf 1,3-5

# Delete page 4 (from 8-page doc)
SumatraPDF clean input.pdf output.pdf 1-3,5-8
```

---

## Reference

For detailed options on less common tools, load `references/advanced-tools.md`.

| Command   | Purpose                                                  |
| --------- | -------------------------------------------------------- |
| `audit`   | Generate HTML report of PDF objects                      |
| `bake`    | Bake annotations and forms into static content           |
| `poster`  | Split large pages into printable tiles                   |
| `recolor` | Change color space (gray, RGB, CMYK)                     |
| `show`    | Inspect internal PDF structure (trailer, xref, objects)  |
| `trim`    | Trim page content to a specified region                  |
| `trace`   | Output drawing commands as XML trace (for debugging)     |
