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
$content = Get-Content "dsm050_final_report.md" -Raw; $body = [regex]::Match($content,'(?s)<!-- WORDCOUNT_START -->(.*?)<!-- WORDCOUNT_END -->').Groups[1].Value; $plain = $body | pandoc -f markdown -t plain; $count = (($plain -split '\s+') | Where-Object { $_ -match '\S' }).Count; $formatted = '{0:N0}' -f $count; $lines = Get-Content "dsm050_final_report.md"; $lines = $lines | ForEach-Object { if ($_ -match '^\s*_\[Word count:.*?words\]_\s*$') { "_[Word count: $formatted words]_" } else { $_ } }; Set-Content "dsm050_final_report.md" $lines -Encoding UTF8; Write-Host "Updated word count in report: $formatted"; pandoc dsm050_final_report.md -o dsm050-report_final.pdf --pdf-engine=xelatex -V geometry:margin=1in --resource-path=.
