# TP03: Interactive Data Analysis and Visualization with Power BI

Module: Big Data Analytics, Master 2 AI & Data Science, University of El Oued, 2026/2027

## Contents

```
TP03/
├── README.md          (this report)
├── TP03.pbit          (Power BI template: report and model WITHOUT data)
├── dashboard.png      (screenshot of the final dashboard)
└── .gitignore         (excludes the raw CSV data)
```

The raw data (SINASC CSV files, about 21.6 million rows) is not included because of its size.
To reopen the report: download the SINASC CSV files for 2017 to 2024, open `TP03.pbit`, and point the folder path to the folder holding the files.

## Dataset

SINASC (Brazilian live-birth information system): one row per birth, 2017 to 2024, about 21.6 million rows after merging.

## Steps performed

### Task 1: Import and explore

1. Opened Power BI Desktop, then Blank report.
2. The data was a folder of CSV files, so I used Get data, Folder.
3. The folder contained two kinds of files (SINASC and unrelated "tabela" files), so I kept only the SINASC files.
4. Opened Transform Data to inspect columns, types, row count and missing values.

### Task 2: Clean and transform (Power Query)

1. Filtered the `Name` column to SINASC files for 2017 to 2024.
2. Combined the files with Combine Files (delimiter: semicolon, which gave correctly separated columns).
3. Kept only the useful columns with Choose Columns, to reduce memory use: Source.Name, CODMUNNASC, CODMUNNATU, CODOCUPMAE, DTNASC, ESCMAE, ESTCIVMAE, GESTACAO, GRAVIDEZ, IDADEMAE, PARTO, QTDFILVIVO, SEXO.
5. Unknown codes (9, 99) and blank values are treated as missing.
6. Close & Apply: 21,613,682 rows loaded.

Note: duplicates were not removed, because the counter column repeats across years and removing duplicates on 21 million rows would be slow and not meaningful.

### Task 3: DAX measures

```DAX
Total Births = COUNTROWS('archive (2)')

Avg Mother Age =
CALCULATE(AVERAGE('archive (2)'[IDADEMAE]), 'archive (2)'[IDADEMAE] < 99)

Avg Children = AVERAGE('archive (2)'[QTDFILVIVO])

Cesarean Births =
CALCULATE(COUNTROWS('archive (2)'), 'archive (2)'[PARTO] = 2)

Cesarean % = DIVIDE([Cesarean Births], [Total Births])
```

Problems fixed along the way:

- A duplicated `Measure =` prefix when pasting a formula (removed the default prefix).
- The column `PESO` was not in the table, so it was replaced by another numeric column.
- Avg Mother Age gave wrong values until unknown ages (99) were excluded.

### Task 4: Visualizations

| Visual | Axis | Value |
|---|---|---|
| Card (KPI) | none | Total Births |
| Line chart | Source.Name (year) | Total Births |
| Bar chart | ESCMAE (mother's education) | Total Births |
| Column chart | PARTO (delivery type) | Total Births |
| Bar chart | ESTCIVMAE (marital status) | Avg Mother Age |

All charts have clear titles.

### Task 5: Interactivity

Added three slicers: year (Source.Name), delivery type (PARTO) and sex (SEXO). The numeric slicers were changed from a range slider to a vertical list.

Tests performed:

- PARTO = 1 and SEXO = 1: the KPI dropped from about 22M to about 5M.
- Year 2019 with PARTO = 2: all charts and the KPI changed accordingly.
- Slicers combine with AND logic and update every visual. Selections were cleared before the final screenshot.

## Findings

- Births declined from about 2.9M (2017) to about 2.4M (2024).
- Cesarean deliveries (about 12.5M, roughly 57%) outnumber vaginal deliveries (about 9.2M).
- Most mothers have secondary education (about 13.4M births, around 62%).
- Average mother age ranges from about 25 to 32 years depending on marital status.

## Short summary

This dashboard analyzes about 21.6 million births recorded in Brazil (SINASC, 2017 to 2024). The KPI card shows total births, and the line chart reveals a decline from about 2.9M in 2017 to about 2.4M in 2024. Most mothers have secondary-level education. Cesarean deliveries (roughly 57%) outnumber vaginal deliveries. The average mother age ranges from about 25 to 32 years. The slicers (year, sex, delivery type) update the KPI card and all charts, confirming the dashboard is interactive. Unknown codes and blank values were treated as missing data.

## Limits

The dashboard describes the data but does not explain the causes. Many columns are numeric codes whose meaning follows the official SINASC data dictionary.
