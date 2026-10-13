# HealthData Studio

A browser-based tool for cleaning, analysing and reporting on patient prescription datasets. Upload a CSV or Excel file, step through a guided workflow, and download the results as a Word document, a PDF report, or a cleaned CSV.

HealthData Studio is a single HTML file (`healthdata_studio.html`). There is nothing to install and no server to run.

---

## Table of Contents

1. [Features](#features)
2. [Getting Started](#getting-started)
3. [Workflow](#workflow)
4. [Input Data Requirements](#input-data-requirements)
5. [Cleaning Methodology](#cleaning-methodology)
6. [Analysis Outputs](#analysis-outputs)
7. [Exporting Results](#exporting-results)
8. [Privacy and Security](#privacy-and-security)
9. [Technology](#technology)
10. [Limitations](#limitations)
11. [Troubleshooting](#troubleshooting)
12. [Project Information](#project-information)

---

## Features

- **Account pages:** register and sign in before using the app.
- **Flexible upload:** CSV, `.xlsx` and `.xls` files, by drag-and-drop or file browser.
- **Automatic column matching:** header names such as `Sex`, `Diagnosis` or `Drug` are recognised, with a manual mapping option.
- **Guided four-step workflow:** each step opens on its own page and can be reached at any time from the step bar.
- **Change dataset anywhere:** every step has a *Change dataset* button. The analysis recalculates and you stay on the same step.
- **Documented cleaning:** text standardisation, duplicate removal and missing-value imputation, each with a stated rationale.
- **Insights:** record totals, unique patients, age metrics, and frequency charts for diseases, medications and gender.
- **Report export:** Word (`.doc`), PDF and cleaned CSV downloads.
- **Local processing:** data is read and analysed in the browser.

---

## Getting Started

### Requirements

- A modern desktop or mobile browser (Chrome, Edge, Firefox or Safari).
- An internet connection the first time the page loads, to fetch three libraries and a web font (see [Technology](#technology)).

### Run the app

1. Save `healthdata_studio.html` to your computer.
2. Double-click the file to open it in your browser.
3. Choose **Create Account**, register, and you will be taken to the upload page.

No build step, package manager or web server is required.

---

## Workflow

| Stage | Purpose |
|---|---|
| **Sign In / Create Account** | Access the workspace. |
| **Upload Data** | Load a file, check the column mapping, and continue. |
| **Step 1: Browse Dataset** | Understand the structure of the data. |
| **Step 2: Clean Up Data** | Remove duplicates and fix missing values. |
| **Step 3: Key Insights** | View totals, distributions and averages. |
| **Step 4: Summary & Export** | Read the key takeaways and download results. |

### Step details

**Step 1: Browse Dataset**
- 1.1 Load and Preview Records: source details and the first 8 records.
- 1.2 Check Column Types: each column classified as identifier, numerical or categorical, with non-null, distinct and missing counts.
- 1.3 Review Summary Statistics: count, mean, median, standard deviation, minimum and maximum for Age.

**Step 2: Clean Up Data**
- 2.1 Detect and Remove Duplicates: the standardisation, missing-ID and duplicate checks, plus a table of removed duplicate records.
- 2.2 Find Missing Information: missing values and percentages per variable.
- 2.3 Fix Missing Data: the method and rationale for each variable, a before/after comparison, and the cleaned dataset.

**Step 3: Key Insights**
- 3.1 Record Count & Totals: total records, unique patients, records per patient, and patients with several records.
- 3.2 Category Frequency & Distribution: top diseases, top medications and gender distribution.
- 3.3 Numerical Averages & Metrics: average and median age, standard deviation and range.

**Step 4: Summary & Export**
- 4.1 Key Takeaways: the main findings in plain sentences.
- 4.2 Save and Share Results: Word, PDF, CSV and copy-summary options.

---

## Input Data Requirements

The app expects one row per prescription record with these fields:

| Field | Meaning | Recognised header names |
|---|---|---|
| Patient ID | Unique identifier for a patient (**required**) | `Patient_ID`, `PatientID`, `ID`, `PID`, `MRN`, `Patient No` |
| Age | Age in years | `Age`, `Age Years` |
| Gender | Patient gender | `Gender`, `Sex` |
| Disease | Diagnosis | `Disease`, `Diagnosis`, `Condition` |
| Medication | Prescribed drug | `Medication`, `Drug`, `Medicine` |
| Dosage | Dose with unit, e.g. `500 mg` | `Dosage`, `Dose` |

- Matching ignores case, spaces and punctuation.
- Only **Patient ID** is mandatory. If another field is missing from your file, the related sections are skipped or marked as unavailable.
- For Excel files, **the first sheet** is used.
- If a header is not recognised, choose the correct column in the **Column mapping** section on the Upload Data page.

### Sample dataset

The **Use sample dataset** button loads a synthetic dataset of 44 records. It deliberately includes duplicates, missing values, inconsistent capitalisation and spacing, an invalid age, and one patient with two different prescriptions, so every cleaning step can be demonstrated. It contains no real patient information.

---

## Cleaning Methodology

Cleaning is applied in a fixed order so that results are consistent.

### 1. Text standardisation
Applied first so that formatting variants are recognised as the same value.
- Leading, trailing and repeated spaces are removed.
- Gender, Disease and Medication are converted to Title Case.
- Dosage is converted to lower case (e.g. `500 MG` becomes `500 mg`).
- Patient IDs are converted to upper case.

### 2. Missing-value detection
The following entries are treated as missing: empty cells, `NA`, `N/A`, `NaN`, `null`, `none` and `-`. Ages that are not numbers, or that fall outside 0 to 120, are also treated as missing.

### 3. Records with no Patient ID
These cannot be linked to a patient and are removed.

### 4. Duplicate removal
A record is a duplicate only if it is **identical in every mapped field**. The first occurrence is kept. A repeated Patient ID with different details (for example, a second prescription) is treated as a genuine repeat visit and is kept.

### 5. Imputation

| Variable | Method | Rationale |
|---|---|---|
| Age | Median of valid ages | Robust to outliers and skewed distributions. |
| Gender | Placeholder `Unknown` | Cannot be inferred reliably; using the most frequent value would inflate one category. |
| Disease | Placeholder `Unknown` | A diagnosis must never be guessed in a clinical dataset. |
| Medication | Placeholder `Not Recorded` | Prescriptions cannot be inferred safely. |
| Dosage | Placeholder `Not Recorded` | Doses are drug- and patient-specific; a typical value could be unsafe. |

Clinical fields are labelled as missing rather than estimated, so no fabricated clinical information enters the dataset.

---

## Analysis Outputs

- **Total records vs. unique patients:** records are rows after cleaning; unique patients are distinct Patient IDs.
- **Average age:** calculated once per unique patient (using the first record of each patient), so patients with several records are not counted more than once.
- **Disease and medication rankings:** use frequency counts of the cleaned data. Placeholder values (`Unknown`, `Not Recorded`) are excluded from rankings, and the number excluded is reported.
- **Dosage:** treated as a categorical variable because units differ between drugs (mg, mcg, units), so numeric comparison across drugs would not be valid.

---

## Exporting Results

Available in **Step 4, section 4.2**.

| Export | Format | Contents |
|---|---|---|
| Word document | `.doc` | Full report with tables and bar charts. Opens in Microsoft Word and compatible editors. |
| PDF report | `.pdf` | Formatted report with cover band, tables, charts and page numbers. |
| Cleaned data | `.csv` | Every cleaned record, using your original column names. |
| Summary text | Clipboard | The key takeaways, ready to paste. |

Notes:
- Word and PDF reports include the first **50** cleaned records. The CSV contains all records.
- The report includes the name of the signed-in user, the date and the dataset name.
- The Word file is an HTML-based `.doc` file, not a modern `.docx`.
- File names are based on the dataset name, for example `patients_report.pdf`.

---

## Privacy and Security

- **Datasets stay in your browser.** Files are read and analysed locally and are never uploaded to a server.
- **Accounts are local.** Registration details are stored in the browser's local storage on the device you use, with passwords salted and hashed (SHA-256). This provides a convenient sign-in for the app but is **not a substitute for server-side authentication**. Anyone with access to the same browser profile and developer tools could remove or alter the stored accounts.
- **Sessions end with the tab.** You stay signed in while the tab is open, and are signed out when it is closed.
- **Accounts do not travel.** An account created in one browser or in one copy of the file at a different location is not available elsewhere. Register again if you move the file.
- **Real patient data:** if you work with identifiable patient records, follow your institution's data-protection rules and ethics approvals. Use de-identified data for coursework and demonstrations.

---

## Technology

- HTML, CSS and vanilla JavaScript in a single file.
- [SheetJS](https://sheetjs.com/) 0.18.5 for reading CSV and Excel files.
- [jsPDF](https://github.com/parallax/jsPDF) 2.5.1 and [jsPDF-AutoTable](https://github.com/simonbengtsson/jsPDF-AutoTable) 3.8.2 for PDF generation.
- IBM Plex Sans via Google Fonts (a system font is used if it cannot load).
- Libraries load from `cdnjs.cloudflare.com`, so an internet connection is needed on first load.

---

## Limitations

- Only the first sheet of an Excel workbook is read.
- Duplicate detection is exact-match only. Near-duplicates (for example, spelling variations of a drug name) are not merged.
- Imputation uses a single median for Age and placeholders for categories. For large amounts of missing data, more advanced methods such as grouped medians or multiple imputation may be preferable.
- On screen, the cleaned dataset preview shows the first 100 records; reports show the first 50.
- Very large files (hundreds of thousands of rows) may be slow, since all processing happens in the browser.
- Results are descriptive. This tool does not provide clinical advice.

---

## Troubleshooting

| Problem | Likely cause and fix |
|---|---|
| *"The PDF library could not load"* | You are offline or the library host is blocked. Connect to the internet and reload the page. |
| File fails to load | Confirm the file is `.csv`, `.xlsx` or `.xls`, is not password-protected, and has a header row with at least one data row. |
| *"Select the Patient ID column"* | No header matched a Patient ID name. Choose the right column in **Column mapping**. |
| Cannot sign in after moving the file | Accounts are tied to the browser and file location. Create the account again. |
| Downloads do nothing | Open the file directly in your browser, not inside an embedded preview, and check that downloads are not blocked. |
| Copy summary fails | The browser blocked clipboard access. Select the Key Takeaways text and copy it manually. |

---

