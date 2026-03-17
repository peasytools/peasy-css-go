# peasy-css-go

[![Go Reference](https://pkg.go.dev/badge/github.com/peasytools/peasy-css-go.svg)](https://pkg.go.dev/github.com/peasytools/peasy-css-go)
[![Go Report Card](https://goreportcard.com/badge/github.com/peasytools/peasy-css-go)](https://goreportcard.com/report/github.com/peasytools/peasy-css-go)
[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](https://opensource.org/licenses/MIT)
[![GitHub stars](https://agentgif.com/badge/github/peasytools/peasy-css-go/stars.svg)](https://github.com/peasytools/peasy-css-go)

Go client for the [PeasyCSS](https://peasycss.com) API -- minify, beautify, and convert CSS units with tools for gradient generation, shadow creation, flexbox layouts, and grid systems. Zero dependencies beyond the Go standard library.

Built from [PeasyCSS](https://peasycss.com), a comprehensive CSS toolkit offering free online tools for minifying, formatting, analyzing, and generating CSS code with detailed property guides, layout references, and a glossary covering modern CSS specifications including Grid, Flexbox, Custom Properties, and Container Queries.

> **Try the interactive tools at [peasycss.com](https://peasycss.com)** -- [CSS Minify](https://peasycss.com/css/css-minify/), [CSS Beautify](https://peasycss.com/css/css-beautify/), [CSS Unit Converter](https://peasycss.com/css/css-unit-converter/), and more.

<p align="center">
  <img src="demo.gif" alt="peasy-css-go demo -- CSS minify, beautify, and unit conversion tools in Go terminal" width="800">
</p>

## Table of Contents

- [Install](#install)
- [Quick Start](#quick-start)
- [What You Can Do](#what-you-can-do)
  - [CSS Optimization Tools](#css-optimization-tools)
  - [Browse CSS Reference Content](#browse-css-reference-content)
  - [Search and Discovery](#search-and-discovery)
- [API Client](#api-client)
  - [Available Methods](#available-methods)
- [Learn More About CSS Tools](#learn-more-about-css-tools)
- [Also Available](#also-available)
- [Peasy Developer Tools](#peasy-developer-tools)
- [License](#license)

## Install

```bash
go get github.com/peasytools/peasy-css-go
```

Requires Go 1.21+.

## Quick Start

```go
package main

import (
	"context"
	"fmt"
	"log"

	peasy "github.com/peasytools/peasy-css-go"
)

func main() {
	client := peasy.New()
	ctx := context.Background()

	// List available CSS tools
	tools, err := client.ListTools(ctx, nil)
	if err != nil {
		log.Fatal(err)
	}
	for _, t := range tools.Results {
		fmt.Printf("%s: %s\n", t.Name, t.Description)
	}
}
```

## What You Can Do

### CSS Optimization Tools

CSS optimization is essential for web performance. Minified CSS reduces file sizes by removing whitespace, comments, and redundant declarations, while beautified CSS restores readability for debugging. The PeasyCSS API provides programmatic access to these transformations alongside unit conversion tools for responsive design workflows.

| Tool | Description | Use Case |
|------|-------------|----------|
| CSS Minify | Remove whitespace and comments from CSS | Production builds, CI/CD pipelines |
| CSS Beautify | Format and indent CSS for readability | Code review, debugging |
| CSS Unit Converter | Convert between px, rem, em, vw, vh | Responsive design systems |

```go
package main

import (
	"context"
	"fmt"
	"log"

	peasy "github.com/peasytools/peasy-css-go"
)

func main() {
	client := peasy.New()
	ctx := context.Background()

	// Fetch the CSS Minify tool details
	tool, err := client.GetTool(ctx, "css-minify")
	if err != nil {
		log.Fatal(err)
	}
	fmt.Printf("Tool: %s\n", tool.Name)           // CSS minification tool
	fmt.Printf("Category: %s\n", tool.Category)   // CSS optimization category

	// List all CSS file formats and their MIME types
	formats, err := client.ListFormats(ctx)
	if err != nil {
		log.Fatal(err)
	}
	for _, f := range formats.Results {
		fmt.Printf("%s (%s): %s\n", f.Name, f.Extension, f.MimeType)
	}
}
```

Learn more: [CSS Minify Tool](https://peasycss.com/css/css-minify/) · [CSS Beautify Tool](https://peasycss.com/css/css-beautify/) · [How to Minify CSS for Production](https://peasycss.com/guides/how-to-minify-css-production/)

### Browse CSS Reference Content

Modern CSS has evolved far beyond simple selectors and properties. Concepts like CSS Grid, Flexbox, Custom Properties (CSS variables), specificity rules, and the cascade determine how styles are applied. The PeasyCSS glossary provides clear definitions and practical examples for these foundational concepts, while guides offer in-depth tutorials on layout techniques and optimization strategies.

| Glossary Term | Description |
|---------------|-------------|
| Flexbox | One-dimensional layout model for distributing space along a row or column |
| Grid | Two-dimensional layout system for rows and columns simultaneously |
| Specificity | Algorithm that determines which CSS rule takes precedence |
| Custom Property | CSS variables defined with `--` prefix, enabling dynamic theming |
| Minification | Process of removing unnecessary characters from CSS without changing functionality |

```go
// Browse CSS glossary terms programmatically
glossary, err := client.ListGlossary(ctx, &peasy.ListOptions{Search: str("flexbox")})
if err != nil {
	log.Fatal(err)
}
for _, term := range glossary.Results {
	fmt.Printf("%s: %s\n", term.Term, term.Definition) // Flexbox layout definition
}

// Read in-depth CSS guides on layout and optimization
guides, err := client.ListGuides(ctx, &peasy.ListGuidesOptions{Category: str("layout")})
if err != nil {
	log.Fatal(err)
}
for _, g := range guides.Results {
	fmt.Printf("%s (%s)\n", g.Title, g.AudienceLevel) // Guide title and difficulty level
}
```

Learn more: [Flexbox Glossary](https://peasycss.com/glossary/flexbox/) · [Grid Glossary](https://peasycss.com/glossary/grid/) · [CSS Grid vs Flexbox Guide](https://peasycss.com/guides/css-grid-vs-flexbox-when-to-use-each/)

### Search and Discovery

The unified search endpoint queries across all CSS tools, glossary terms, guides, and file formats simultaneously. This is useful for building IDE integrations, documentation search, or CLI tools that need to surface relevant CSS content based on user queries.

```go
// Search across all CSS tools, glossary, and guides
results, err := client.Search(ctx, "gradient generator", nil)
if err != nil {
	log.Fatal(err)
}
fmt.Printf("Found %d tools, %d glossary terms\n",
	len(results.Results.Tools),
	len(results.Results.Glossary)) // Cross-content CSS search results
```

Learn more: [Specificity Glossary](https://peasycss.com/glossary/specificity/) · [Custom Property Glossary](https://peasycss.com/glossary/custom-property/) · [All CSS Guides](https://peasycss.com/guides/)

## API Client

The client wraps the [PeasyCSS REST API](https://peasycss.com/developers/) with typed Go structs and zero external dependencies.

```go
client := peasy.New()
// Or with a custom base URL:
// client := peasy.New(peasy.WithBaseURL("https://custom.example.com"))
ctx := context.Background()

// List tools with pagination
tools, _ := client.ListTools(ctx, &peasy.ListOptions{Page: 1, Limit: 10})

// Get a specific tool by slug
tool, _ := client.GetTool(ctx, "css-minify")
fmt.Println(tool.Name, tool.Description)

// Search across all content
results, _ := client.Search(ctx, "gradient generator", nil)
fmt.Printf("Found %d tools\n", len(results.Results.Tools))

// Browse the glossary
glossary, _ := client.ListGlossary(ctx, &peasy.ListOptions{Search: str("flexbox")})
for _, term := range glossary.Results {
	fmt.Printf("%s: %s\n", term.Term, term.Definition)
}

// Discover guides
guides, _ := client.ListGuides(ctx, &peasy.ListGuidesOptions{Category: str("layout")})
for _, g := range guides.Results {
	fmt.Printf("%s (%s)\n", g.Title, g.AudienceLevel)
}

// List file format conversions
conversions, _ := client.ListConversions(ctx, &peasy.ListConversionsOptions{Source: str("css")})

// Get format details
format, _ := client.GetFormat(ctx, "css")
fmt.Printf("%s (%s): %s\n", format.Name, format.Extension, format.MimeType)
```

Helper for optional string parameters:

```go
func str(s string) *string { return &s }
```

### Available Methods

| Method | Description |
|--------|-------------|
| `ListTools(ctx, opts)` | List tools (paginated, filterable) |
| `GetTool(ctx, slug)` | Get tool by slug |
| `ListCategories(ctx, opts)` | List tool categories |
| `ListFormats(ctx, opts)` | List file formats |
| `GetFormat(ctx, slug)` | Get format by slug |
| `ListConversions(ctx, opts)` | List format conversions |
| `ListGlossary(ctx, opts)` | List glossary terms |
| `GetGlossaryTerm(ctx, slug)` | Get glossary term |
| `ListGuides(ctx, opts)` | List guides |
| `GetGuide(ctx, slug)` | Get guide by slug |
| `ListUseCases(ctx, opts)` | List use cases |
| `Search(ctx, query, limit)` | Search across all content |
| `ListSites(ctx)` | List Peasy sites |
| `OpenAPISpec(ctx)` | Get OpenAPI specification |

Full API documentation at [peasycss.com/developers/](https://peasycss.com/developers/).
OpenAPI 3.1.0 spec: [peasycss.com/api/openapi.json](https://peasycss.com/api/openapi.json).

## Learn More About CSS Tools

- **Tools**: [CSS Minify](https://peasycss.com/css/css-minify/) · [CSS Beautify](https://peasycss.com/css/css-beautify/) · [CSS Unit Converter](https://peasycss.com/css/css-unit-converter/) · [All Tools](https://peasycss.com/)
- **Guides**: [CSS Grid vs Flexbox](https://peasycss.com/guides/css-grid-vs-flexbox-when-to-use-each/) · [How to Minify CSS for Production](https://peasycss.com/guides/how-to-minify-css-production/) · [All Guides](https://peasycss.com/guides/)
- **Glossary**: [Flexbox](https://peasycss.com/glossary/flexbox/) · [Grid](https://peasycss.com/glossary/grid/) · [Minification](https://peasycss.com/glossary/minification/) · [Specificity](https://peasycss.com/glossary/specificity/) · [Custom Property](https://peasycss.com/glossary/custom-property/) · [All Terms](https://peasycss.com/glossary/)
- **Formats**: [All Formats](https://peasycss.com/formats/)
- **API**: [REST API Docs](https://peasycss.com/developers/) · [OpenAPI Spec](https://peasycss.com/api/openapi.json)

## Also Available

| Language | Package | Install |
|----------|---------|---------|
| **Python** | [peasy-css](https://pypi.org/project/peasy-css/) | `pip install "peasy-css[all]"` |
| **TypeScript** | [peasy-css](https://www.npmjs.com/package/peasy-css) | `npm install peasy-css` |
| **Rust** | [peasy-css](https://crates.io/crates/peasy-css) | `cargo add peasy-css` |
| **Ruby** | [peasy-css](https://rubygems.org/gems/peasy-css) | `gem install peasy-css` |

## Peasy Developer Tools

Part of the [Peasy Tools](https://peasytools.com) open-source developer ecosystem.

| Package | PyPI | npm | Go | Description |
|---------|------|-----|----|-------------|
| peasy-pdf | [PyPI](https://pypi.org/project/peasy-pdf/) | [npm](https://www.npmjs.com/package/peasy-pdf) | [Go](https://pkg.go.dev/github.com/peasytools/peasy-pdf-go) | PDF merge, split, rotate, compress -- [peasypdf.com](https://peasypdf.com) |
| peasy-image | [PyPI](https://pypi.org/project/peasy-image/) | [npm](https://www.npmjs.com/package/peasy-image) | [Go](https://pkg.go.dev/github.com/peasytools/peasy-image-go) | Image resize, crop, convert, compress -- [peasyimage.com](https://peasyimage.com) |
| peasy-audio | [PyPI](https://pypi.org/project/peasy-audio/) | [npm](https://www.npmjs.com/package/peasy-audio) | [Go](https://pkg.go.dev/github.com/peasytools/peasy-audio-go) | Audio trim, merge, convert, normalize -- [peasyaudio.com](https://peasyaudio.com) |
| peasy-video | [PyPI](https://pypi.org/project/peasy-video/) | [npm](https://www.npmjs.com/package/peasy-video) | [Go](https://pkg.go.dev/github.com/peasytools/peasy-video-go) | Video trim, resize, thumbnails, GIF -- [peasyvideo.com](https://peasyvideo.com) |
| **peasy-css** | [PyPI](https://pypi.org/project/peasy-css/) | [npm](https://www.npmjs.com/package/peasy-css) | [Go](https://pkg.go.dev/github.com/peasytools/peasy-css-go) | **CSS minify, format, analyze -- [peasycss.com](https://peasycss.com)** |
| peasy-compress | [PyPI](https://pypi.org/project/peasy-compress/) | [npm](https://www.npmjs.com/package/peasy-compress) | [Go](https://pkg.go.dev/github.com/peasytools/peasy-compress-go) | ZIP, TAR, gzip compression -- [peasytools.com](https://peasytools.com) |
| peasy-document | [PyPI](https://pypi.org/project/peasy-document/) | [npm](https://www.npmjs.com/package/peasy-document) | [Go](https://pkg.go.dev/github.com/peasytools/peasy-document-go) | Markdown, HTML, CSV, JSON conversion -- [peasyformats.com](https://peasyformats.com) |
| peasytext | [PyPI](https://pypi.org/project/peasytext/) | [npm](https://www.npmjs.com/package/peasytext) | [Go](https://pkg.go.dev/github.com/peasytools/peasytext-go) | Text case conversion, slugify, word count -- [peasytext.com](https://peasytext.com) |

## License

MIT
