------------------------------------------------------------------------

editor: markdown: wrap: 72 ---

# Marine Mammal Stock Assessment Report (SAR) Template

<!-- Add badges once the workflow is live and the org path is settled: [![Render](https://github.com/ORG/REPO/actions/workflows/publish.yml/badge.svg)](https://github.com/ORG/REPO/actions/workflows/publish.yml)-->

A Quarto template for authoring marine mammal Stock Assessment Reports under the Marine Mammal Protection Act, consistent with the NMFS [*Guidelines for Assessing Marine Mammal Stocks* (GAMMS IV)](https://www.fisheries.noaa.gov/national/marine-mammal-protection/guidelines-assessing-marine-mammal-stocks).

Generate an institutional repository from this template, add your stock's assessment text and supporting data, and render a formatted PDF. Document layout, NOAA Fisheries branding, and citation formatting are preconfigured, and result tables follow GAMMS Section 3.2 formatting conventions.

> If you're new to GitHub, see the [NMFS GitHub Guide](https://nmfs-opensci.github.io/GitHub-Guide/index.html) for the basics of cloning, branching, and committing. Software installation (R, RStudio, Quarto) is covered in [Before you start](#before-you-start) below.

## Contents

- [Outcome](#Outcome)
- [Before you start](#before-you-start)
- [Quick start](#quick-start)
- [Repository tour](#repository-tour)
- [Writing your SAR](#writing-your-sar)
- [Rendering](#rendering)
- [Staying current with the template](#staying-current-with-the-template)
- [Accessibility](#accessibility)
- [Troubleshooting](#troubleshooting)
- [Getting help](#getting-help)
- [Contributing](#contributing)
- [Disclaimer](#disclaimer)

------------------------------------------------------------------------

## Outcome {#outcome}

Rendering the template produces a formatted SAR PDF that includes:

- A NOAA Fisheries cover page and running headers, populated from the YAML header
- Automatic line numbering for review drafts
- Citations and a reference list generated from a CSL JSON reference file
- Numbered figures and tables, cross-referenced by label (`@fig-`, `@tbl-`)
- Nmin, PBR, and strategic-status values calculated from the stock data file per GAMMS Section 3.2, rather than typed into the text by hand
- An HTML preview for drafting

The template includes two example stocks (`pakicetus-stockA`, `pakicetus-stockB`) built on invented data. Render both examples first. A clean render confirms your environment is working, so you have a baseline to debug against.

------------------------------------------------------------------------

## Before you start {#before-you-start}

Install the following before generating a repository from this template. Version minimums are enforced: PDF output is produced through Typst, which requires Quarto 1.7 or later.

| Tool    | Minimum    | Where                                  |
|---------|------------|----------------------------------------|
| R       | 4.2        | <https://cran.r-project.org>           |
| RStudio | 2023.06+   | <https://posit.co/downloads>           |
| Quarto  | **1.7**    | <https://quarto.org/docs/get-started/> |
| Git     | any recent | <https://git-scm.com/install>          |

> **Versions are subject to change.** Quarto and Typst both release frequently, so treat the minimums above as a snapshot rather than a fixed floor, and make sure to verify with `quarto check` if a render behaves unexpectedly.

You do **not** need a LaTeX installation. PDF output is produced through [Typst](https://quarto.org/docs/output-formats/typst.html), a modern typesetting system bundled with Quarto — no separate install required — that compiles significantly faster than LaTeX. The report layout itself is defined in the `_extensions/` directory as a Quarto custom Typst format; authors do not need to write or edit Typst code to produce a SAR.

### Verify your installation

Run this in a terminal. All four should print a version:

``` bash
quarto --version
git --version
quarto check
```

`quarto check` is the one that matters. It confirms Quarto can locate your R installation and knitr. Resolve any reported failures before continuing.

On Windows, `R --version` may fail because R is not on the system PATH by default. This is expected; `quarto check` reports the R version it detects.

------------------------------------------------------------------------

## Quick start {#quick-start}

> **Decision point.** This section assumes each stock gets its own repository, generated directly from this template via GitHub's **Use this template** button. Where those repositories are hosted (organization, visibility, naming convention), and whether template generation is even the intended workflow, is for NOAA Fisheries leadership to determine. The `ORG` placeholder in step 2 and the steps below should be finalized once that decision is made.

**1. Generate your repository.** Select **Use this template**, then **Create a new repository** at the top of this page.

Name it for the stock it covers, for example `harbor-porpoise-gom-sar`.

**2. Clone the repository.**

``` bash
git clone https://github.com/ORG/your-new-repo.git
cd your-new-repo
```

**3. Install R dependencies.** From the repository root, in R:

``` r
install.packages("pak")
pak::local_install_deps()
```

This reads `DESCRIPTION` and installs everything the template requires.

**4. Render the example reports.**

``` bash
quarto render
```

The first render is slower while R caches packages. Output is written to \[OUTPUT DIR\]. Open the PDF and confirm the cover page, line numbering, and reference list all resolve.

**5. Start writing.** See [Writing your SAR](#writing-your-sar) below.

------------------------------------------------------------------------

## Repository tour {#repository-tour}

> **Subject to change.** This reflects the template's structure as of the current version as folders and files may be added, removed, or reorganized as the template develops.

| Path | What it is | Do you edit it? |
|----|----|----|
| `pakicetus-stockA/`, `pakicetus-stockB/` | Example stock reports | Delete once you've copied one |
| `data/` | Placeholder folder for supporting data files, including a `spatial_data/` subfolder for maps and geographic data | Yes — add your stock's data files here |
| `scripts/` | Build and validation scripts | Rarely — currently empty, reserved for future use |
| `R/` | Shared functions, auto-sourced from every `.qmd`'s setup chunk | Rarely — the auto-source mechanism is live, though `R/` has no functions in it yet |
| `assets/` | SCSS theme, CSL citation style, fonts, per-stock `references_<stock>.json` | `references_<stock>.json` yes, rest no |
| `_extensions/noaa-afsc/nmfs-sar-pdf/` | Typst PDF format definition | **No** — see below |
| `_quarto.yml` | Project config, render list, navigation | **Yes** — add your stock |
| `DESCRIPTION` | R dependency manifest, read by `pak::local_install_deps()` | Rarely — only if you add a new R package dependency |
| `.github/workflows/` | Automated rendering (`publish_pakicetus.yml`) and secret scanning (`gitleaks_scan.yml`) | Rarely |
| `LICENSE`, `LICENSE.md`, `CODE_OF_CONDUCT.md`, `DISCLAIMER.md`, `.gitignore`, `.Rbuildignore`, `*.Rproj` | Repository boilerplate and governance files | No |

**On `_extensions/`:** this directory defines the NOAA SAR page layout. Editing it locally means your report drifts from every other region's, and your changes get overwritten on the next template update. If the layout is wrong for your stock, \[open an issue\] instead.

------------------------------------------------------------------------

## Writing your SAR {#writing-your-sar}

### 1. Create your stock directory

Copy an existing example rather than starting from an empty file. This preserves a working YAML header, which is the most error prone part of setup.

``` bash
cp -r pakicetus-stockA harbor-porpoise-gulfofmaine
mv harbor-porpoise-gulfofmaine/pakicetus-stockA.qmd \
   harbor-porpoise-gulfofmaine/harbor-porpoise-gulfofmaine.qmd
```

Then register the new file in `_quarto.yml`, under `render:` and `navbar:`:

``` yaml
project:
  render:
    - index.qmd
    - harbor-porpoise-gulfofmaine/harbor-porpoise-gulfofmaine.qmd

website:
  navbar:
    left:
      - file: harbor-porpoise-gulfofmaine/harbor-porpoise-gulfofmaine.qmd
        text: Harbor porpoise - Gulf of Maine
```

Add to these lists rather than replacing them. A file omitted from `render:` is skipped silently, with no error.

### 2. Fill in the YAML header

The YAML header is the block between the `---` fences at the top of your `.qmd`. It supplies the cover page content, the author list, and the formatting options. (subject to improve)

``` yaml
---
title: "Harbor Porpoise (Phocoena phocoena): Gulf of Maine/Bay of Fundy Stock"
stock-name: "Gulf of Maine/Bay of Fundy"
common-name: "Harbor porpoise"
genus-species: "Phocoena phocoena"
nmfs-region: "NEFSC"
sar-year: 2026
linenumbering: true      # set false for the final published version
format: nmfs-sar-pdf-typst
bibliography: ../assets/references.bib
---
```

Every field is required. A blank `stock-name` renders a blank cover page rather than an error, so check the PDF.

### 3. Enter your stock data

Edit `data/stocks/your-stock.yml`. This file is the authoritative source for every value the report calculates. Derived quantities are computed from it and should never be typed into the prose.

``` yaml
stock_name: "Gulf of Maine/Bay of Fundy"
abundance:
  n_best: 95543
  cv_n: 0.31
productivity:
  r_max: 0.04          # GAMMS default for cetaceans
recovery_factor: 0.5
human_caused_msi:
  annual_total: 164
status:
  esa: "not listed"
  depleted: false
```

``` yaml
# proposed updates on yaml
common_name:     Harbor porpoise
scientific_name: Phocoena phocoena
stock_name:      "Gulf of Maine/Bay of Fundy"
region:          NEFSC
sar_year:        2026

abundance:
  n_best:      95543      # best available abundance estimate
  cv_n:        0.31       # coefficient of variation of n_best
  survey_year: 2021       # year of the most recent survey

productivity:
  r_max:       0.04       # GAMMS default for cetaceans

recovery_factor: 0.5      # F_r, between 0.1 and 1.0

human_caused_msi:
  annual_total: 164       # mean annual human-caused mortality and serious injury

status:
  osp:              unknown      # within / below / unknown
  esa:              not listed   # not listed / threatened / endangered
  population_trend: unknown      # increasing / stable / declining / unknown
```

Regenerate the derived values and validate:

``` bash
Rscript scripts/build_stock_summary.R
Rscript scripts/check_data.R
```

`check_data.R` recomputes every derived value and reports any that do not reproduce from the inputs. Run it before each commit.

### 4. Write the sections

The report follows the section order given in GAMMS Appendix C. Write the content in Markdown, using level-two headings for each section.

``` markdown
## Stock Definition and Geographic Range

Harbor porpoise are found in the Gulf of Maine year-round
[@palka2020; @hayes2021].

## Population Size

The best available abundance estimate is `r stocks$n_best`
(CV = `r stocks$cv_n`).
```

Inline R code, written as `` `r ... ` ``, retrieves values from the stock data file at render time. Use it for every quantity that also appears in the data file. When an estimate is revised, the text updates on the next render. Values typed directly into the prose are the most common source of disagreement between a SAR's narrative and its tables.

## 5. Add figures and tables

Figures and tables are added directly in each stock's `.qmd` file, using `ggplot2` for figures and `gt` for tables. Write the code for each figure or table in the section of the report it supports. For example, a range map belongs in "Stock Definition and Geographic Range"; a PBR summary table belongs in "Potential Biological Removal".

Make sure `ggplot2` and `gt` are loaded in the report's setup chunk:

```{r}
#| label: setup
#| include: false

library(ggplot2)
library(gt)
```

### Adding a figure

1.  Create a code chunk where the figure belongs in the text.
2.  Set chunk options: `label` (prefixed `fig-`), `fig-cap` (the printed caption), and `fig-alt` (a text description for screen readers).
3.  Write the `ggplot2` code and let the plot print as the chunk's last line.

```{r}
#| label: fig-range
#| fig-cap: "Distribution of the Gulf of Maine/Bay of Fundy stock."
#| fig-alt: "Map of the Gulf of Maine showing survey effort and sightings."

ggplot(range_data, aes(x = lon, y = lat)) +
  geom_point() +
  theme_minimal()
```

### Adding a pre-made figure (from a PNG)

If a figure already exists as an image file, it can be inserted directly instead of generating it with `ggplot2`. Create a folder named `figures` at the root of the repository, place the image file and referecne it with `knitr::include_graphics()` :

```{r}
#| label: fig-range
#| fig-cap: "Distribution of the Bottlenose Dolphin, Western North Atlantic stock."
#| fig-alt: "Map of the western North Atlantic showing survey effort and sighting density for the Bottlenose Dolphin stock."
#| out-width: "90%"
#| fig-align: "center"

knitr::include_graphics("../figures/BND_WNA_range_map.png")
```

A caption can also be built dynamically from variables computed earlier in the report, rather than typed as a fixed string. This is useful when a caption needs to update automatically each report cycle, for example a mortality time series whose end year and PBR value change every year:

```{r}
#| label: fig-mortality
#| fig-cap: !expr paste0("Estimated annual human-caused mortality and serious injury, 1990-", year_max - 1, ". Red dashed line indicates PBR (", pbr_value, " animals per year).")
#| fig-alt: "Time series plot of estimated human-caused mortality and serious injury, with a red dashed reference line showing PBR."
#| out-width: "90%"
#| fig-align: "center"

knitr::include_graphics(paste0("../figures/CommonDolphin_WNA_mortality_1990-", year_max, ".png"))
```

The same requirements still apply: `label`, `fig-cap`, and `fig-alt` are all needed, since alt text describes what the image conveys, not how it was produced. A few notes on these examples:

- Store pre-made images in the `figures` folder at the project root and reference them with a relative path (`../figures/...`), the same pattern already used for `../assets/` in the YAML front matter.

- `!expr` tells Quarto to evaluate the R code that follows rather than treat it as a literal string, so the caption can be built from values such as `year_max` or `pbr_value` instead of being retyped each cycle. In-text citations, such as `@Wade_1998`, still resolve normally inside a caption written this way.

- `out-width` and `fig-align` control the image's size and placement on the page, since `include_graphics()` does not have `ggplot2`'s own sizing options.

### Adding a table

Tables follow the same pattern, using a `tbl-` label and `tbl-cap` instead of `fig-cap`.

<!-- details to be added regarding accessibility for tables if needed -->

```{r}
#| label: tbl-pbr
#| tbl-cap: "Values used to calculate PBR."

pbr_values |>
  gt() |>
  cols_label(Parameter = "", Value = "")
```

Add a page break after wide or tall tables so they don't run into the next section:

### Referencing figures and tables in text

Use `@fig-label` or `@tbl-label` to cross-reference a figure or table from your prose. Quarto turns this into a clickable link to the figure/table wherever it's referenced.

> As shown in @fig-range, sightings concentrate ...

`fig-cap` supplies the printed caption. `fig-alt` supplies the description read by screen readers and is required for Section 508 compliance. See [Accessibility](#accessibility).

### 6. Add citations

Two files in `assets/` control citations:

| File                        | Purpose                               |
|-----------------------------|---------------------------------------|
| `references_pakicetus.json` | Reference entries, in CSL JSON format |
| `apa.csl`                   | Citation style. Do not edit           |

Your stock's `.qmd` header points at the first of these:

``` yaml
bibliography: ../assets/references_your-stock.json
```

Copy `references_pakicetus.json`, rename it for your stock, and add entries.

#### Building the reference file

<!-- Details regarding usage of Zotero needs to be checked by authors/editors. -->

Zotero with the Better BibTeX plugin is the recommended workflow. Right-click the collection, choose Export Collection, and select CSL JSON. Check "Keep updated" so Zotero rewrites the file as the collection changes. Save it to `assets/` and update the `bibliography:` line to match.

If you already have a \`.bib\` file, import it into Zotero and export the collection as CSL JSON.

JSON is unforgiving about punctuation, so export rather than hand-edit where possible.

#### Entry format

Each entry is an object in a single array. The `id` is the citation key.

``` json
[
  {
    "id": "Wade_1998",
    "type": "article-journal",
    "title": "Calculating limits to the allowable human-caused mortality of cetaceans and pinnipeds",
    "author": [
      {
        "family": "Wade",
        "given": "Paul R."
      }
    ],
    "container-title": "Marine Mammal Science",
    "volume": "14",
    "issue": "1",
    "page": "1-37",
    "issued": {
      "date-parts": [[1998]]
    }
  }
]
```

Use `"type": "article-journal"` for journal articles and `"type": "report"` for NOAA Technical Memoranda. Corporate authors take `"literal"` in place of `"family"` and `"given"`:

``` json
"author": [
  {"literal": "National Marine Fisheries Service"}
]
```

#### Citing in the text

| Syntax                        | Renders as                         |
|-------------------------------|------------------------------------|
| `[@Wade_1998]`                | (Wade, 1998)                       |
| `@Wade_1998`                  | Wade (1998)                        |
| `[@Wade_1998; @Martien_2019]` | (Wade, 1998; Martien et al., 2019) |

#### The reference list

The list is generated at render time from the citations present in the document, so uncited entries are omitted. Keep the placement marker under the section heading:

``` markdown
## REFERENCES CITED
 
::: {#refs}
:::
```

A citation key with no matching entry does not stop the render. It prints in place as `(Wade_1998?)` and pandoc logs a `citation not found` warning, so check the render log as well as the output.

------------------------------------------------------------------------

## Rendering {#rendering}

| Command | Output | When |
|----|----|----|
| `quarto preview` | Live HTML, auto-refreshes | While drafting |
| `quarto render` | Everything in `_quarto.yml` | Before committing |
| `quarto render path/to/stock.qmd` | One report | Faster iteration |

Draft in HTML preview — it's seconds rather than minutes. Render the PDF before every commit, because Typst catches layout problems that HTML silently tolerates.

Set `linenumbering: false` and re-render for the final published version.

------------------------------------------------------------------------

## Staying current with the template {#staying-current-with-the-template}

Repositories generated from a template have no automatic link back to it. To pull in updates (Josh's 2026 cycle changes, for example):

``` bash
git remote add template https://github.com/noaa-afsc/nmfs-sar-template-repo.git
git fetch template
git merge template/main --allow-unrelated-histories
```

`--allow-unrelated-histories` is needed because template generation starts a fresh history. The first merge will report conflicts in files you've edited — keep your versions of the `.qmd` and data files, take the template's version of `_extensions/` and `R/`.

Do this on a branch, not on `main`. (expand here)

------------------------------------------------------------------------

## Accessibility {#accessibility}

Published SARs must meet [Section 508](https://www.section508.gov/test/documents/) (WCAG 2.0 A/AA).

**Built in:** this repo builds its PDFs through Quarto's `nmfs-sar-pdf-typst` extension, which compiles to Typst. Typst tags PDF output automatically, and Quarto sends each figure's `fig-alt` chunk option straight to Typst as alt text. That means document structure, heading hierarchy, reading order, and citation formatting all happen without extra work. ([Quarto's Typst accessibility guide](https://quarto.org/docs/output-formats/typst.html))

*Note for template maintainers:* automatic tagging is not the same as full PDF/UA‑1 compliance. Tagging happens either way, but PDF/UA‑1 export is opt-in. Setting `pdf-standard: ua-1` under the `nmfs-sar-pdf-typst` format options turns on Typst's accessibility checker at compile time, which catches problems like missing titles, wrong heading order, and missing alt text (per [Typst's 0.14 release notes](https://typst.app/blog/2025/typst-0.14/)). Quarto passes this option straight to the Typst compiler; no extra tooling is needed. Set it once in `_quarto.yml` as a project-level default rather than repeating it in every `.qmd`, so new stock directories inherit it automatically.

**Your to-do list** (adapted from [ASAR's Accessibility Guide](https://nmfs-ost.github.io/asar/articles/accessibility_guide.html)):

1.  **Set `fig-alt` on every figure.** Good alt text covers four things: the chart type, the axis variables, the data range, and the relationship between variables, what the figure is actually showing. That last part matters most and gets skipped most often. See ASAR's [guidance and resources](https://nmfs-ost.github.io/asar/articles/accessibility_guide.html#guidance-and-resources) for worked examples and prompts by figure type (line graphs, Kobe plots, confidence intervals).
2.  **Follow the general accessibility rules.** Give tables real header rows. Don't rely on color alone, if strategic status is shown in red, also label it "strategic" in the text. Write link text that describes the destination, not "click here."
3.  **Test the rendered PDF with a screen reader** before publishing. Confirm the reading order makes sense and that alt text is actually read aloud. **Resources**

- [NOAA Section 508 Accessibility Checklist](https://library.noaa.gov/ld.php?content_id=61618926): a step-by-step PDF checklist
- [NOAA "Big 5" Quick Start](https://library.noaa.gov/Section508/QuickStart): bookmarks, alt text, reading order, document properties, tagging
- [Section 508: Alternative Text](https://www.section508.gov/create/alternative-text/): what counts as good alt text
- [Quarto Typst Accessibility Guide](https://quarto.org/docs/output-formats/typst.html): automatic tagging, `fig-alt` pass-through, the `pdf-standard` option
- [Screen Readers Overview (AFB)](https://www.afb.org/blindness-and-low-vision/using-technology/assistive-technology-products/screen-readers): how screen readers read a document

------------------------------------------------------------------------

## Troubleshooting {#troubleshooting}

| Symptom | Likely cause | Fix |
|----|----|----|
| `quarto: command not found` | Quarto is not on your PATH | Reinstall Quarto and restart your terminal. |
| `Format not found: nmfs-sar-pdf-typst` | Quarto could not find `_extensions/` from where the command was run | Render from the repo root, or use your IDE's Render button, which does this automatically. |
| `could not find function "your_function"` | A custom function in `R/` was not sourced, usually because the file was not saved there or was renamed | Confirm the setup chunk is present and unmodified; it sources every `.R` file in `R/` automatically. |
| `object 'x' not found` | A script that builds that object has not been run yet | Run the relevant script in `scripts/` before rendering. |
| Blank cover page fields | Empty values in the YAML header | Fill in every field at the top of the `.qmd`. |
| Figures missing from the PDF | A relative path (like `../`) broke when Quarto changed the working directory | Use `here::here()` for file paths, the same pattern the setup chunk already uses for the `R/` folder. |
| Citations render as `[@key]` | The key is not in your bibliography file, or is misspelled | Bibliography files here are CSL-JSON (for example, `references_pakicetus.json`), not `.bib`. Check spelling; keys are case-sensitive. |
| First render is very slow | R is installing packages for the first time, including the large `rnaturalearthhires` dataset package | Expected once; later renders are faster. |

------------------------------------------------------------------------

## Getting help {#getting-help}

- **Bugs and template problems** — [Issues](../../issues)
- **Questions about using it** — [Discussions](../../discussions)
- **GAMMS and science questions** — your regional SAR editor
- **General R/Quarto help** — NOAA Open Science help desk <!--resource link-->

------------------------------------------------------------------------

## Contributing {#contributing}

Improvements are welcome — especially from SAR authors who hit something confusing. If the template tripped you up, it will trip up the next person.

See [CONTRIBUTING.md](CONTRIBUTING.md). In short: open an issue first for anything structural, work on a branch, and make sure `quarto render` succeeds before opening a pull request.

All contributors are expected to follow the [Code of Conduct](CODE_OF_CONDUCT.md).

------------------------------------------------------------------------

## Disclaimer {#disclaimer}

This repository is a scientific product and is not official communication of the National Oceanic and Atmospheric Administration, or the United States Department of Commerce. All NOAA GitHub project code is provided on an 'as is' basis and the user assumes responsibility for its use. Any claims against the Department of Commerce or Department of Commerce bureaus stemming from the use of this GitHub project will be governed by all applicable Federal laws. Any reference to specific commercial products, processes, or services by service mark, trademark, manufacturer, or otherwise, does not constitute or imply their endorsement, recommendation, or favoring by the Department of Commerce. The Department of Commerce seal and logo, or the seal and logo of a DOC bureau, shall not be used in any manner to imply endorsement of any commercial product or activity by DOC or the United States Government.
