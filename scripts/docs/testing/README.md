# Testing update automation
_Currently being re-written_

[![Python 3"](https://img.shields.io/badge/python-3.7|3.8|3.9-blue.svg?&logo=python&logoColor=yellow)](https://www.python.org/downloads/release/python-3)
[![Contribute](https://img.shields.io/badge/-contribute-0055ff)](CONTRIBUTE.md)
[![Data](https://img.shields.io/badge/public-data-purple)](../../../public/data/)


Testing data is updated several times a week. For some countries, the update is done using an automated process,
while others require some manual work. 

### Content
1. [Testing pipeline files](#1-testing-pipeline-files)
2. [Development environment](#2-development-environment)
3. [The data pipeline](#3-the-data-pipeline)
4. [Contribute](CONTRIBUTE.md)
## 1. Testing pipeline files

The [testing directory](../../scripts/testing) contains the following files:


| File name      | Description |
| ----------- | ----------- |
| [`automated_sheets/`](../../scripts/testing/automated_sheets)      | Temporary automated imports are placed here.       |
| [`automations/`](../../scripts/testing/automations)      | Scripts to automate country data imports.       |
| [`input/`](../../scripts/testing/input)      | Input data pipeline configuration.       |
| [`auto_quick_collect.sh`](../../scripts/testing/auto_quick_collect.sh), [`collect_data.sh`](../../scripts/testing/collect_data.sh)      |  Bash scripts to generate the dataset    |
| [`attempts.R`](../../scripts/testing/attempts.R),  [`generate_dataset.R`](../../scripts/testing/generate_dataset.R), [`generate_html.R`](../../scripts/testing/generate_html.R), [`replace_audited_metadata.R`](../../scripts/testing/replace_audited_metadata.R), [`run_r_scripts.R`](../../scripts/testing/run_r_scripts.R), [`smoother.R`](../../scripts/testing/smoother.R), [`run_python_scripts.py`](../../scripts/testing/run_python_scripts.py)      | R/Python individual scripts to generate the testing dataset.       |
| [`grapher_annotations.txt`](../../scripts/testing/grapher_annotations.txt),[`source_table.html`](../../scripts/testing/source_table.html )       | Other output files.       |
| [`test_update.sh.template`](../../scripts/testing/test_update.sh.template)      | Template to push testing update changes.       |

_*Only most relevant files have been listed_ 


## 2. Development environment
To update the data, make sure you follow the steps below.

### Python and R
Make sure you have a working environment with R and python 3 installed. We recommend R >= 4.0.2 and Python >= 3.7.

### Install python requirements
In your environment (shell), run:

```
$ pip install -r requirements.txt
```

### Install R requirements
In your R console, run:

```r
install.packages(c("data.table", "googledrive", "googlesheets4", "httr", "imputeTS", "lubridate", "pdftools", "retry", 
                   "rjson", "rvest", "stringr", "tidyr", "rio", "plyr", "bit64"))
```

Note: `pdftools` requires `poppler`. In MacOS, run `brew install poppler`.

### Configuration file (internal)

Create a file `testing_dataset_config.json` with all required parameters:

```json
{
    "google_credentials_email": "[OWID_MAIL]",
    "covid_time_series_gsheet": "[COVID_TS_GSHEET]",
    "attempted_countries_ghseet": "[COUNTRIES_GSHEET]",
    "audit_gsheet": "[AUDIT_GSHEET]",
    "owid_cloud_table_post": "[OWID_TABLE_POST]"
}
```

## 3. The data pipeline

### Manual data updates
For some countries, the process is automated. These countries, while present in our dataset do not under
[`automated_sheets/`](../../scripts/testing/automated_sheets).

### Automated process

#### Get the data
Run the following commands to run the batch and incremental updates. It will then generate individual country files and
save them in [`automated_sheets/`](../../scripts/testing/automated_sheets).

For python scripts:
```bash
python3 run_python_scripts.py [option]
```

For R scripts:
```bash
Rscript run_r_scripts.R [option]
```

Note: Accepted values for `option` are: "quick" and "update". The "quick" option automatically runs twice a day
and is pushed to the repo by @edomt. A complete execution with `mode=update` is run three times a week:
- Monday, Friday by @camappel
- Wednesday by @lucasrodes


#### Generate dataset

Run `generate_dataset.R`. Usage of RStudio is recommended for easier debugging.

#### Export final files and update website

Create your own version of [`test_update.sh.template`](../../scripts/testing/test_update.sh.template), adapted to your local paths, and run it to update the COVID megafile, and push the testing update to the repo.
