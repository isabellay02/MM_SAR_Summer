------------------------------------------------------------------------

editor: markdown: wrap: 72 ---

# Marine Mammal Stock Assessment Report (SAR) Template

<!-- Add badges once the workflow is live and the org path is settled: [![Render](https://github.com/ORG/REPO/actions/workflows/publish.yml/badge.svg)](https://github.com/ORG/REPO/actions/workflows/publish.yml)-->

A Quarto repository template for authoring marine mammal Stock Assessment Reports under the Marine Mammal Protection Act, following the NMFS [*Guidelines for Assessing Marine Mammal Stocks* (GAMMS IV)](https://www.fisheries.noaa.gov/national/marine-mammal-protection/guidelines-assessing-marine-mammal-stocks).

Generate a repository from this template, edit the Quarto source files for your stock, and render a formatted PDF. Document layout, NOAA Fisheries branding, citation formatting, and the calculations specified in Section 3.2 are preconfigured. Authors supply the assessment text and the stock data file; the template derives the reported values and handles formatting.

> **New to Quarto, R, or Git?** Begin with the [instruction website](LINK), which covers software installation and basic version control. This README assumes a working installation and familiarity with standard Git operations.

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

- A NOAA Fisheries cover page and running headers populated by the YAML header
- Automatic line numbering for review drafts
- Citations and a reference list generated from a CSL JSON reference file
- Numbered figures and tables referenced by label (`@fig-`, `@tbl-`)
- Values from N<sub>min</sub>, PBR calculated from the stock data file following GAMMS section 3.2 and strategic-status calculations derived from your data, not typed in by hand
- An optional HTML preview for iteration while drafting

The template includes two complete example stocks (`pakicetus-stockA`, `pakicetus-stockB`) using invented data. **Render both before making any changes**. A successful render confirms that the local environment is configured correctly and isolates any subsequent failure to the author's own edits.

------------------------------------------------------------------------

## Before you start {#before-you-start}

Install the following before generating a repository from this template. Version minimums are enforced: PDF output is produced through Typst, which required Quarto 1.5 or latter.

| Tool    | Minimum    | Where                                        |
|---------|------------|----------------------------------------------|
| R       | 4.2        | <https://cran.r-project.org>                 |
| RStudio | 2023.06+   | <https://posit.co/download/rstudio-desktop/> |
| Quarto  | **1.7**    | <https://quarto.org/docs/get-started/>       |
| Git     | any recent | <https://git-scm.com/downloads>              |

You do **not** need a LaTeX installation. This template renders PDFs through [Typst](https://quarto.org/docs/output-formats/typst.html), a typesetting system distributed with Quarto, so no additional software is needed beyond the four items above. The report layout is defined in the `_extensions/` directory as a Quarto custom format. Authors do not need to modify Typst code to write a SAR.

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

## Quick start (set up private repo) {#quick-start}

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

## Repository tour (subject to update for updated template) {#repository-tour}

| Path | What it is | Do you edit it? |
|----|----|----|
| `pakicetus-stockA/` | Example stock report | Delete once you've copied it |
| `data/stocks/*.yml` | Per-stock inputs (abundance, M/SI, status) | **Yes** — this is where your numbers go |
| `data/*.csv` | Generated summary tables | No — built by `scripts/` |
| `scripts/` | Build and validation scripts | Rarely |
| `R/` | Shared functions, auto-sourced by each report | Rarely |
| `assets/` | SCSS theme, CSL citation style, fonts, `references.bib` | `references.bib` yes, rest no |
| `_extensions/noaa-afsc/nmfs-sar-pdf/` | Typst PDF format definition | **No** — see below |
| `_quarto.yml` | Project config, render list, navigation | **Yes** — add your stock |
| `.github/workflows/` | Automated rendering | Rarely |

**On `_extensions/`:** this directory defines the NOAA SAR page layout. Editing it locally means your report drifts from every other region's, and your changes get overwritten on the next template update. If the layout is wrong for your stock, [open an issue](../../issues) instead.

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

### 5. Add figures and tables (under active development)

```` markdown
```{r}
#| label: fig-range
#| fig-cap: "Distribution of the Gulf of Maine/Bay of Fundy stock."
#| fig-alt: "Map of the Gulf of Maine showing survey effort and sightings."

plot_stock_range(stock = "harbor-porpoise-gom")
```

As shown in @fig-range, sightings concentrate ...
````

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

Published SARs must meet Section 508 requirements. Some of this is built in; some needs you.

**Built in:** document structure and heading hierarchy, tagged headings, reading order, citation formatting.

**Your responsibility:**

1.  **Alt text on every figure** — `fig-alt` in the chunk options. Describe what the figure *shows*, not that it is a figure. "Map of the Gulf of Maine with 47 sighting locations concentrated near Jeffreys Ledge," not "Map of study area." (example here)
2.  Add figure (adding jpeg/png)
3.  **Table headers** — use proper header rows so screen readers can announce them.
4.  **Don't encode meaning in color alone** — if strategic status is red, also label it "strategic."
5.  **Meaningful link text** — not "click here."

```{=html}
<!-- TODO: confirm with Josh whether the Typst output has been tested
     against a screen reader, and link to the NOAA 508 checklist. -->
```

------------------------------------------------------------------------

## Troubleshooting {#troubleshooting}

| Symptom | Likely cause | Fix (add details) |
|----|----|----|
| `quarto: command not found` | Quarto not on PATH | Reinstall; restart terminal |
| `Format not found: nmfs-sar-pdf-typst` | Ran from wrong directory | Render from the repo root |
| `could not find function "calc_pbr"` | `R/` not sourced | Check the setup chunk is present and unmodified |
| `object 'stocks' not found` | Data not built | Run `scripts/build_stock_summary.R` |
| Blank cover page fields | Empty YAML values | Fill every field in the header |
| Figures missing from PDF | Wrong relative path | Use `here::here()`, not `../` |
| Citations render as `[@key]` | Key not in `.bib` | Check spelling; keys are case-sensitive |
| First render very slow | Downloading Typst | Expected once |

Still stuck? Include your `quarto check` output and the full error when you [open an issue](../../issues).

------------------------------------------------------------------------

## Getting help {#getting-help}

- **Bugs and template problems** — [Issues](../../issues)
- **Questions about using it** — [Discussions](../../discussions)
- **GAMMS and science questions** — your regional SAR editor
- **General R/Quarto help** — NOAA Open Science help desk (resource link)

------------------------------------------------------------------------

## Contributing {#contributing}

Improvements are welcome — especially from SAR authors who hit something confusing. If the template tripped you up, it will trip up the next person.

See [CONTRIBUTING.md](CONTRIBUTING.md). In short: open an issue first for anything structural, work on a branch, and make sure `quarto render` succeeds before opening a pull request.

All contributors are expected to follow the [Code of Conduct](CODE_OF_CONDUCT.md).

------------------------------------------------------------------------

## Disclaimer {#disclaimer}

This repository is a scientific product and is not official communication of the National Oceanic and Atmospheric Administration, or the United States Department of Commerce. All NOAA GitHub project code is provided on an 'as is' basis and the user assumes responsibility for its use. Any claims against the Department of Commerce or Department of Commerce bureaus stemming from the use of this GitHub project will be governed by all applicable Federal laws. Any reference to specific commercial products, processes, or services by service mark, trademark, manufacturer, or otherwise, does not constitute or imply their endorsement, recommendation, or favoring by the Department of Commerce. The Department of Commerce seal and logo, or the seal and logo of a DOC bureau, shall not be used in any manner to imply endorsement of any commercial product or activity by DOC or the United States Government.
