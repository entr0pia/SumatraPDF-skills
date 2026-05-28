# SumatraPDF Advanced Tools

Less common CLI tools. Load this reference when the core tools don't cover the task.

## audit

Generate an HTML report about PDF objects.

```
SumatraPDF audit [options] input.pdf [input2.pdf ...]
  -o -    output file
```

```bash
SumatraPDF audit -o report.html input.pdf
```

---

## bake

Convert annotations and form widgets into static PDF content.

```
SumatraPDF bake [options] input.pdf [output.pdf]
  -A      keep annotations
  -F      keep forms
  -O -    comma separated list of output options
```

```bash
SumatraPDF bake input.pdf output.pdf
```

---

## poster

Split large PDF pages into tiles for printing and assembling.

```
SumatraPDF poster [options] input.pdf [output.pdf]
  -p -    password
  -m -    margin (overlap) between pages (pts, or %)
  -x      x decimation factor
  -y      y decimation factor
  -r      split right-to-left
```

```bash
# Split each page into 4 tiles (2x2)
SumatraPDF poster -x 2 -y 2 input.pdf output.pdf
```

---

## recolor

Change the color space of a PDF.

```
SumatraPDF recolor [options] <input filename>
  -c -    Output colorspace (gray(default), rgb, cmyk)
  -r      Remove OutputIntent(s)
  -o -    Output file
```

```bash
SumatraPDF recolor -o output.pdf -c gray input.pdf
```

---

## show

Inspect internal PDF structure (trailer, xref, pages, outline, objects).

```
SumatraPDF show [options] file.pdf ( trailer | xref | pages | grep | outline | js | form | <path> ) *
  -p -    password
  -o -    output file
  -e      leave stream contents in their original form
  -b      print only stream contents, as raw binary data
  -g      print only object, one line per object, suitable for grep
  -r      force repair before showing any objects
  -L      show object labels
  path: path to an object, starting with either an object number,
          'pages', 'trailer', or a property in the trailer;
          path elements separated by '.' or '/'. Path elements must be
          array index numbers, dictionary property names, or '*'.
```

```bash
SumatraPDF show input.pdf trailer
SumatraPDF show input.pdf pages
SumatraPDF show input.pdf 3
```

---

## trim

Trim PDF page content to a box (MediaBox, CropBox, etc.) or add margins.

```
SumatraPDF trim [options] <input filename>
  -b -    Which box to trim to (MediaBox(default), CropBox, BleedBox, TrimBox, ArtBox)
  -m -    Add margins to box (+ve for inwards, -ve outwards).
                  <All> or <V>,<H> or <T>,<R>,<B>,<L>
  -e      Exclude contents of box, rather than include them
  -f      Fallback to mediabox if specified box not available
  -o -    Output file
```

```bash
SumatraPDF trim -b CropBox -o output.pdf input.pdf
```

---

## trace

Print detailed drawing commands in XML format (for debugging PDF rendering).

```
SumatraPDF trace [options] file [pages]
  -p -    password
  -b -    use named page box (MediaBox, CropBox, BleedBox, TrimBox, or ArtBox)
  -W -    page width for EPUB layout
  -H -    page height for EPUB layout
  -S -    font size for EPUB layout
  -U -    file name of user stylesheet for EPUB layout
  -X      disable document styles for EPUB layout
  -d      use display list
  pages   comma separated list of page numbers and ranges
```

```bash
SumatraPDF trace input.pdf 1
```
