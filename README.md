# DSM050 Data Visualisation — Reported Road Collisions in Great Britain, 2025

Final coursework: visual analysis of the Department for Transport Road Safety Open Data, investigating temporal, geographical, road-environment and
environmental patterns in reported personal-injury road collisions across Great Britain during 2025.

## Structure
- `notebook/` — full analysis notebook (executed, all outputs embedded)
- `data/` — source and derived datasets used in the analysis
- `figures/` — all figures used in the report
- `report/` — final PDF report

## Dataset
Department for Transport (DfT) Road Safety Open Data: Collisions, 2025.

The dataset contains police-reported personal-injury road collisions recorded through the STATS19 reporting system across England, Scotland and Wales.

## Reproducing the report

The final PDF report can be generated from the Markdown source using Pandoc and XeLaTeX:

```bash
pandoc report/dsm050_final_report.md -o report/dsm050-report_final.pdf \
  --pdf-engine=xelatex \
  -V geometry:margin=1in \
  --resource-path=.
