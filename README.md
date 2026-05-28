# SumatraPDF Skills for AI Agent

A specialized skill for AI agents to perform PDF operations using [SumatraPDF](https://www.sumatrapdfreader.org/) command-line tools (requires [pre-release 3.7+](https://www.sumatrapdfreader.org/prerelease)).

## Skills

### [sumatrapdf-skill](skills/sumatrapdf-skill/SKILL.md)
Comprehensive PDF manipulation via SumatraPDF CLI:
- **Text Extraction**: Extract text from PDFs as plain text, HTML, or structured text (XML/JSON)
- **Image Extraction**: Extract embedded images and fonts from PDF files
- **Text Search**: Search for text across PDFs with regex support
- **Merge & Split**: Combine multiple PDFs or extract/delete specific pages
- **Compression**: Reduce PDF file size with aggressive compression options
- **Encryption**: Encrypt PDFs with AES-256, RC4-128, or decrypt protected files
- **Format Conversion**: Convert PDF to PNG, SVG, HTML, and more
- **PDF Inspection**: View fonts, images, page geometry, and internal structure

## Installation

```bash
npx skills add https://github.com/entr0pia/SumatraPDF-skills
```

The agent will automatically recognize the skill and use it for PDF operations.

## Usage

The agent will trigger the skill when you ask:
- "Extract all text from this PDF"
- "Merge these 3 PDFs into one"
- "Search for 'budget' in this PDF and tell me which pages"
- "Compress this PDF to reduce file size"
- "Extract images from this PDF"
- "Convert this PDF to SVG"
- "Encrypt this PDF with a password"
- "Delete pages 3-5 from this PDF"

## Project Structure

```
SumatraPDF-skills/
├── skills/
│   └── sumatrapdf-skill/      # Core SumatraPDF CLI skill
│       ├── SKILL.md
│       └── references/        # Advanced tool documentation
│           └── advanced-tools.md
└── LICENSE
```

## License

GPL-3.0
