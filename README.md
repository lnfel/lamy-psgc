# Lamy PSGC

Lamy PSGC is a tool to extract PSGC Publication data into JSON format.

## Motivation

Currently there is no API for querying Philippines geographical data. There have been numerous attempts online that I discovered but most are outdated and are not working. This project opens a way for developers to simply download the latest geographical data published by PSGC and extract the data needed and host the data in some database of sort for their purpose.

## Requirements for the script to work

- Python3 (Recommended)
- openpyxl installed using pip3
- PSGC Publication Excel file (the file can be downloaded at https://psa.gov.ph/classification/psgc/)

## How to use

1. Download or clone the repo
```s
git clone https://github.com/lnfel/lamy-psgc.git
```
2. Put the PSGC Publication Excel file inside the same directory with `extract-psgc-to-json.py` script.
3. Open the directory in command prompt or terminal of your choice
4. Run the python script
```s
# Usage
python3 ./extract-psgc-to-json.py path-to-excel-file
# Sample (Wrap the path in double quotes if file has spaces)
python3 ./extract-psgc-to-json.py "./PSGC 4Q 2022 Publication Datafile.xlsx"
```
5. The json files will be generated in `./databases` folder in the current directory.
  - database/regions.json
  - database/provinces.json
  - database/municipalities.json
  - database/districts.json
  - database/cities.json
  - database/barangays.json

  Extracted data will also contain the following attributes where appropriate. It helps if one will be using a relational database.
  - `code` Code of the area
  - `region_code` Code of the region for Provinces, Mun/City and Bgy
  - `province_code` Code of the province for Mun/City and Bgy
  - `municipality_code` Code of the mun/city for Bgy

There is also another geographical level that is not included with the extraction. It is abbreviated as `SGU` on PSGC sheet, I honestly don't know what it meant so I skipped extracting it. It includes the following:
- Carmen Cluster
- Kabacan Cluster
- Midsayap Cluster I
- Midsayap Cluster II
- Pigcawayan Cluster
- Pikit Cluster I
- Pikit Cluster II
- Pikit Cluster III

Feel free to modify the script if you need the SGU.

## Importing data to Fauna database
This part is optional and only exists merely as a note to myself when learning Fauna the first time.
1. Create free account on Fauna website
2. After logging in, create a new database. Name it however you want.
3. Create a database key. Go to Security > Database Keys > New Key to generate one. This will be used for authenticating to `Fauna shell`. Don't forget to keep the generated `secret` in a safe place.
4. Install `Fauna shell`
```s
npm install -g fauna-shell
```
5. Login to `Fauna shell`
```s
fauna cloud-login
# The endpoint alias prefix (to combine with a region): cloud
# How do you prefer to authenticate? Secret
# Secret (from a key or token): Enter the secret/database key here
# Select a region Classic
```

### Importing single json file
```s
fauna import --path=./regions.json
```

### Import JSON data to an existing collection
```s
fauna import --path=./regions.json --collection=regions
# If collection has data, we can append the imported data using --append flag
fauna import --path=./regions.json --collection=regions --append
```

### Importing multiple files
> Note: This can handle large datasets... Just WOW!

Create a folder and put all files to be imported inside
```s
fauna import --path=./source_files
# In our case
fauna import --path=./database
```

### Links
- Learn Fauna database (video from Fireship)
  - https://www.youtube.com/watch?v=2CipVwISumA
- Importing multiple files
  - https://fauna.com/blog/data-import