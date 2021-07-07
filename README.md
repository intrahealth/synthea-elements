# synthea-elements

This repo contains multilingual names for the periodic elements and 'table', a simple non-country location. This dataset is used for creating multilingual, non-US (or any country-specific) names and a single location for Synthea based (poorly) on the simple UK example in synthea-international (not the full UK entry).

The Jupyter notebook contains the code used to generate the names. The current languages are Arabic, Chinese, English, French, Spanish, and Russian. Names can be generated for any language that is supported by the Google Translate API. Google Cloud SDK and a project with Translate API-enabled must be set-up to support using the API. See the Google Cloud docs. 

## Usage

To use in a Synthea project, copy over the files into Synthea (this only adds files):

```sh
# clone main synthea repo if not done already
git clone https://github.com/synthetichealth/synthea
git clone https://github.com/intrahealth/synthea-elements
cd synthea-elements
# copy files from synthea-elements into synthea main repo
# cp -r src <path to synthea folder> # recursive with lowercase -r to keep src folder
# cp -r src ~/src/github.com/synthetichealth/synthea/
cp -r src ../synthea/
```

Backup and copy a new names file into place, this example uses Arabic names:
```sh
# cd ~/src/github.com/synthetichealth/synthea/
cd ../synthea
# backup original names file
cp src/main/resources/names.yml src/main/resources/names.yml.bak
cp src/main/resources/arabic-names.yml src/main/resources/names.yml
```

Run synthea with names and location using custom config to override for periodic table
```sh
./run_synthea -p 5 "Elements" -c src/main/resources/uk-synthea.properties
```

```txt
Location: Elements
Min Age: 0
Max Age: 140
4 -- السيليكون311 كبريت452 (4 y/o F) Table, Elements
3 -- التيلوريوم769 هيدروجين419 (12 y/o F) Table, Elements
2 -- الفلور479 لورنسيوم205 (13 y/o M) Table, Elements
1 -- السماريوم398 البورون986 (30 y/o M) Table, Elements DECEASED
5 -- النبتونيوم958 النيوبيوم982 (43 y/o F) Table, Elements
1 -- الكادميوم864 أستاتين31 (59 y/o M) Table, Elements
Records: total=6, alive=5, dead=1
```

### Caveats

* Why are there names in both `english` and `spanish` entries in the names files regardless of language? Synthea distributes names unevenly based on a a US demographic model. See this [issue](https://github.com/synthetichealth/synthea/issues/908#issuecomment-868730712). In order for all names to have equal probability, the hack is to put all names for any language are duplicated under `english` and `spanish` even if there are no English or Spanish names in the names file.

### Todo

* Use another country code or non-country code.
* Change other nouns

## DIY single-city location

The example in synthea-international (not the full UK country info) can be used to replace and create another single-city location as the user prefers.

Clone synthea-international and copy over the example:
```sh
git clone https://github.com/synthetichealth/synthea-international
cd synthea-international
# this replaces the periodic elements location with the original UK location
cp -r example/ ~/src/github.com/intrahealth/synthea-elements/
```

In the example from synthea-international, note the geographies:

* City: Shropshire
* County: Shrewsbury
* State: West Midlands
* State abbreviation: WMS

Determine the city of choice, county, and state. In this repo:

* City: Periodic
* County: Table
* State: Elements
* State abbreviation: Elements

Use a text editor or the command line to find and replace instances of the city, county, state, and state abbreviation in the CSVs in src/main. For example, from the root of this repo on Unix-like systems:

```sh
CSV=$(find src/main -iname "*.csv")
echo $CSV
for FILE in $CSV; do sed -i '' 's/Shropshire/Periodic/g' $FILE; done
for FILE in $CSV; do sed -i '' 's/Shrewsbury/Table/g' $FILE; done
for FILE in $CSV; do sed -i '' 's/West Midlands/Elements/g' $FILE; done
for FILE in $CSV; do sed -i '' 's/WMS/Elements/g' $FILE; done
```

If a mistake is made, simple copy back over the original example from synthea-international.
```sh
# from root of synthea-international
cp -r example/ ~/src/github.com/intrahealth/synthea-elements/
```

Copy the changed files into synthea.
```sh
# copy files from synthea-elements into synthea main repo
# cp -r src <path to synthea folder> # recursive with lowercase -r to keep src folder
# cp -r src ~/src/github.com/synthetichealth/synthea/
cp -r src ../synthea/
```

In synthea, copy over a names file if it hasn't already been done:
```sh
# cd ~/src/github.com/synthetichealth/synthea/
cd ../synthea
cp src/main/resources/arabic-names.yml src/main/resources/names.yml
```

Run Synthea with the example data with names substituted:

```sh
# from synthea repo
# cp src/main/resources/names.yml src/main/resources/names.yml.bak
./run_synthea -p 5 "Elements" -c src/main/resources/uk-synthea.properties
```

It is not necessary to use the uk-synthea-properties, they can be changed from the command line:

```bash
./run_synthea -p 5 -s 123 "Elements" \
--generate.demographics.default_file geography/demographics_uk.csv \
--generate.geography.zipcodes.default_file geography/zipcodes_uk.csv \
--generate.geography.timezones.default_file geography/timezones_uk.csv \
--generate.geography.country_code UK \
--generate.payers.insurance_companies.default_file payers/insurance_companies_uk.csv \
--generate.payers.insurance_companies.medicare "National Health Service" \
--generate.payers.insurance_companies.medicaid "National Health Service" \
--generate.payers.insurance_companies.dual_eligible "National Health Service" \
--exporter.years_of_history 0 \
--exporter.baseDirectory ./output \
--exporter.fhir.use_us_core_ig false
```
