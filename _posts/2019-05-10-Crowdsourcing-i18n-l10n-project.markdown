---
layout: post
title:  "Crowdsourcing i18n and l10n project (wxWidgets)"
date:   2019-05-10 14:34:51 -0700
categories: wxWidgets c++ i18n l10n 
author: Xun Li
---
# GeoDa I18n and L10n Project

## Project Description

GeoDa Internationalization (I18n) and Localization(L10n) project aims to provide an online tool that GeoDa users could help to translate the GeoDa UI to different languages.

For crowdsourcing, we use Google Spreadsheet with the public address [here](https://docs.google.com/spreadsheets/d/1iZa4wCIyTDlIRYoW7229YoZWKZ0lmIiOFsCJG3ZVw-s/edit?usp=sharing). Anyone can access this spreadsheet, and edit each translation.

This infrastructure has scripts and codes to create, update and maintain GeoDa’s language packages from Google Sheets automatically.  

## How to contribute

Please visit Google Sheet [here](https://docs.google.com/spreadsheets/d/1iZa4wCIyTDlIRYoW7229YoZWKZ0lmIiOFsCJG3ZVw-s/). 

1. Click on the sheet that you want to help to translate (see the table below)

| Language | Sheet Name |
|----------|------------|
| Spanish | es |
| 中文 |	zh_CN |
| Deutsch | de |
| 日本語	| jp |

   * Column A contains the English messages that need to be translated (not editable)
   * Column B you can contribute by inputting proper translation in each cell
   * Column C you can input your email, GitHub alias, etc. so we can include your name in GeoDa's contribution list

2. Copy (Ctrl+C or Cmd+C) an item from Column A, and paste it (Ctrl+V or Cmd+V) to the left cell in Column B

3. Edit the translation in the left cell (Please keep the bracket [ ])

**Demo**

[[https://github.com/lixun910/images/blob/master/geoda_features/i18n_demo.gif]]

**Note**
* Empty spaces should be reserved in translation
* Placeholders (no need to translate): %d %s %f \t \n 
* Symbols (no need to translate): >  < == =


## Embedded this Google Spreadsheet to a website

<iframe src="https://docs.google.com/spreadsheets/d/e/2PACX-1vRRrZqXc7n6oCksp3xxgP-dRCqh_koMMCYu7ES2pZZ3MVJv_6YVIpMqWsLXeUxvMPy3M1iJ9aQQ_DlT/pubhtml?widget=true&amp;headers=false"></iframe>

## Background

One of the most classic tools for I18n and L10n is a Unix tool called Gettext, which is used in this project. There are three file types you usually deal with while working with Gettext.  PO (Portable Object), MO (Machine Object) files and POT (PO Template) file. 

```
I18n
|
├──lang
|   ├──config.ini
|   ├── es
│   |    └── geoda.mo
|   └── zh_CN
│         └── geoda.mo
|
├──profile
│   ├── es.mo
│   └── zh_CN.po
|
└── geoda.pot
```
>The related files can be found under geoda_source_dir/internationalization. 

The above example shows the directory name (e.g. `es/`, `zh_CN/` following the `Locale Code`, which is simply a code that identifies one version of a language. It’s defined following the [ISO 639-1](https://en.wikipedia.org/wiki/ISO_639-1) and [ISO 3166-1 alpha-2](https://en.wikipedia.org/wiki/ISO_3166-1) specs: two lower-case letters for the language, optionally followed by an underscore and two upper-case letters identifying the country or regional code.

## For Development

Before every GeoDa release, please follow the procedures below to create language packages for GeoDa.

### Get latest translated items from Google

The following scripts get the latest translations of Chinese (zh_CN) and Spanish (es) from Google Sheets, and save the results to PO files, and last creat MO files for GeoDa.

```Shell
python3 google_sheets_tool.py zh_CN --output ./pofiles/zh_CN.po

python3 google_sheets_tool.py es --output ./pofiles/es.po
```
**NOTE** After creating PO files from Google Sheets, the best practice is to use Github Desktop to check and verify all changes in current PO files.

[[https://github.com/lixun910/images/blob/master/geoda_features/i18n_verify_github.png]]

After verifying all changes in PO files, one can commit the changes and push the new PO files to Github repo. Then, one can use `msgfmt` to create MO files from PO files:

```Shell
msgfmt ./pofiles/zh_CN.po -o ./lang/zh_CN/GeoDa.mo

msgfmt ./pofiles/es.po -o ./lang/es/GeoDa.mo
```

### Update Google Sheets from source code

Ongoing development will generate new UI items that need to be translated. One can use the following scripts to update PO files

#### 1.1 Create geoda.pot file from source files

To create a geoda.pot file, one can run the shell script under geoda_source_dir/internationalzation directory:
```Shell
./1_create_geoda_pot.sh
```
This script will go through all the *.cpp and *.h files under geoda_source_dir directory, and extract the strings coded in `_()` macro.
```Shell
# 1_create_geoda_pot.sh
# the first part will use find() command to find _() strings from 
find .. \( -name '*.cpp' -o -name '*.h' \) -not -path "../BuildTools/*" | xargs xgettext -d geoda -s --keyword=_ -p ./ -o geoda.pot
```

#### 1.2 Create xrc.pot file from source files

There are also strings defined in various xrc files (e.g. `menu.xrc`, `dialogs.xrc` etc). There is a python script `gen_po_from_xrc.py` that can be used to create a `xrc.pot` file.

```Shell
python3 2_gen_po_from_xrc.py
```
#### 1.3 Update existing PO file

Update existing PO file (e.g. zh_CN.po, specified using argument --input) using POT files (geoda.pot and xrc.pot extracted from source code. New msgid with empty msgstr will be added into existing PO file and saved into a new file specified by argument --output')

```Shell
python3 3_updatePO.py ./geoda.pot ./xrc.pot --input ./pofiles/es.po --output ./new_es.po

python3 3_updatePO.py ./geoda.pot ./xrc.pot --input ./pofiles/zh_CN.po --output ./new_zh_CN.po
```

**NOTE** Please manually compare the new PO file with existing PO file to verify all newly added UI items. For example, using `vimdiff` to compare `./new_zh_CN.po` with `./pofiles/zh_CN.po`:

[[https://github.com/lixun910/images/blob/master/geoda_features/i18n_newPO_cmp_old.png]]

#### 1.4 Create a CSV file that will be uploaded to Google Sheet to replace the current one

```Shell
python po2csv.py ./new_es.po ./es.csv

python po2csv.py ./new_zh_CN.po ./zh_CN.csv
```

#### 1.5 Upload CSV file to Google Sheets

Please manually check CSV file before uploading it to Google Sheets. The name of the CSV file should be the same with the name of the Sheet, which should be a locale name (e.g. `zh_CN.csv`)

When uploading a CSV file, please select the Sheet that you want to update ((e.g. `zh_CN`). Then, use File->Import menu, and choose `Upload` tab, and select the CSV file from your local drive. The options should be configured as below:

[[https://github.com/lixun910/images/blob/master/geoda_features/i18n_upload_google.png]]

