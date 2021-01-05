![](https://www.vera.org/dist/img/logo_full.svg)

# ICE-Reported COVID-19 Detention Dataset
Data on confirmed COVID-19 cases and tests as reported by Immigration and Customs Enforcement (ICE) from March 24, 2020 through present. This data originates from hourly downloads of [https://ice.gov/coronavirus](https://ice.gov/coronavirus). Vera updates the data in this repository on a daily basis.

# About the Data
On March 24, 2020, ICE launched a [webpage](https://ice.gov/coronavirus) that
compiles information about COVID-19 in detention facilities, including the reported numbers of confirmed COVID-19 cases and tests for people detained by ICE. Researchers at the Vera Institute of Justice immediately began routinely archiving the
page data, on a daily basis at first and then hourly as of April 17, 2020 at 5:00 PM EDT.

While ICE typically updates the numbers of cumulative and current cases for people in detention on this page about once per weekday, it replaces previously reported numbers rather than presenting them longitudinally, making it difficult to track how the numbers of confirmed COVID-19 cases and tests administered have changed over time. The datasets included in this repository present selected data from these archived page
downloads over time in four domains:

1. "Facility cases": The number of confirmed COVID-19 cases among people in detention per facility
1. "National cases": The number of confirmed COVID-19 cases among people in detention nationally
1. "National population": The current number of people in ICE detention nationally
1. "National tests": The number of COVID-19 tests reportedly administered to detained people

These data all pertain to people detained by ICE; while the page also lists case data
for ICE employees at detention centers, these statistics are not regularly updated by ICE and do not include other facility staff.

As this dataset is intended to make transparent what ICE has reported on COVID-19
over time, the data are presented "as is" and reflect what ICE reported on the
webpage for each given download, including any errors in what ICE has reported.
For example, at Immigration Centers of America Farmville, a decrease in cumulative
cases was reported: on the page archived 2020-07-13 23:01:00 (`page_id`:
`9d206de59d1e1aad42e49ca5c7f6cc60`), the page listed 106 cumulative cases; on
the page archived 2020-07-15 14:01:00 (`page_id`: `df376692b19409cacac5d990e742b44f`),
315 cases were reported; on the page archived 2020-07-25 01:03:00, this cumulative
case count *decreased* to 289 (`page_id`: `3a805c09ad6c87fa5507ded71b3eaa0f`).

### ICE Webpage Versions
While the content of the ICE webpage changes roughly once per weekday, the page layout
has undergone 3 major revisions affecting the data available and how it is parsed, outlined below:

|Version |First Appearance   |Description                                                                                                                |Example|
|:-------|:------------------|:--------------------------------------------------------------------------------------------------------------------------|:-------------------------------------|
|V1      |2020-03-24 22:47:00|Original page layout. ICE included all information in a single "tab," reporting the national and facility-level number of confirmed cases. Test and population information not yet included.|[Link](https://vera-cij-public.s3.us-gov-east-1.amazonaws.com/ice_covid/ice_detention_covid/ICE%20Guidance%20on%20COVID-19%20V1.html)|
|V2      |2020-04-22 17:01:00|ICE began reporting the number of tests administered nationally within its webpage, reported in text as: "As of %m %d, ICE has administered {N} tests." Additionally, ICE partitions case data into a separate tab, "Confirmed Cases"                       |[Link](https://vera-cij-public.s3.us-gov-east-1.amazonaws.com/ice_covid/ice_detention_covid/ICE%20Guidance%20on%20COVID-19%20V2.html)|
|V3      |2020-04-28 22:01:00|ICE began reporting test information in its own container and began reporting the currently detained population in text.|[Link](https://vera-cij-public.s3.us-gov-east-1.amazonaws.com/ice_covid/ice_detention_covid/ICE%20Guidance%20on%20COVID-19%20V3.html)|
|V4      |2020-06-01 21:01:00|Current page layout. ICE created a separate tab, "ICE Detainee Statistics," for all COVID-19 summary statistics for people in detention.|[Link](https://vera-cij-public.s3.us-gov-east-1.amazonaws.com/ice_covid/ice_detention_covid/ICE%20Guidance%20on%20COVID-19%20V4.html)|

### ICE Webpage Timestamps
Vera cautions users from relying solely on the
the listed and available timestamps on a given archived page due to inconsistencies and issues observed. Instead, we recommend using the `page_downloaded` timestamp (that is, the date and time when Vera archives the webpage) alongside other timestamps provided by ICE.

The `ice_date_updated` timestamp, which on its face seems to be automatically
generated, does not maintain fidelity to this expectation in all cases. For
example, while the page data was known to have been updated between `page_id`
`0d14fa23d8f6d79b9be212468c473164` (archived 2020-03-30 21:48) and `page_id`
`bb9d58d22b788cd20ff41b5e15c1e55e` (archived 2020-04-02 01:24), this timestamp
does not change.

The "AS OF" page timestamp has been subject to similar issues. For example, the page archived on 2020-06-30 18:01
(`page_id`: `8f0aba53265c8fd4012fdb8f19765ff8`) lists the "AS OF" date as "6/2/2020,"
an apparent mistyping of "6/29/2020."

All timestamps within these data files have been converted to UTC.

### Facility Name Standardization and Geolocation
A pervasive issue encountered in the archived pages is an inconsistency in how ICE refers to unique detention facilities. The
`facility_lookup` table shows how Vera recodes the detention facility strings from all archived pages to consistent,
standardized names with assigned unique identifiers. As new facilities appear on
the page, `facility_id` and geolocation data for these entries may not be
immediately available.

Vera uses the following sources to standardize facility strings:

1. **Locations table from The Marshall Project** ("MP"): First, Vera attempts to match detention facilities reported by ICE to facilities in a [facility locations table](https://github.com/themarshallproject/dhs_immigration_detention/blob/master/locations.csv) produced by The Marshall Project,  which draws from a November 2017 [detention facilities dataset](https://immigrantjustice.org/ice-detention-facilities-november-2017) obtained by the Immigrant Legal Resource Center (ILRC) through the Freedom of Information Act (FOIA). Where a facility is matched to this locations table, Vera assigns "DETLOC" in the locations table as a unique identifier and uses the "lat" and "long" fields from the lookup table.

1. **ICE.gov Facility List** ("Web"): Where a facility cannot be matched to this locations table, Vera attempts to match detention facilities to ICE's [list of detention centers](https://ice.gov/detention-facilities). If a match is found, Vera uses the facility webpage link as the unique identifier and uses the latitude and longitude associated with the address listed.

1. **Other** ("Other"): If no match is found within either of these two sources, or where a facility is reported as "local hospital" or other medical care facility, Vera manually assigns an identifier and location data.

# Data Dictionaries

We include in this repository data on the four domains above in two versions:

- "`_hourly`" These tables contain parsed data from all page archives available, and are made available as [Apache Parquet](https://parquet.apache.org/) files due to file size limitations in GitHub.
- "`_daily`": These tables are subsets of the corresponding `data_hourly/` tables. Rather than including records for all archived pages for a given day, these tables retain only the records associated with the latest page_downloaded timestamp for a given day (defined as 0:00 to 23:59 Eastern Time).

The directory tree below outlines the organization,
followed by data dictionaries for each data file.

```
ice-detention-covid/
    |-- README.md
    |-- License.md
    |-- data_hourly/
    |   |-- facility_cases_hourly.parquet
    |   |-- national_cases_hourly.parquet
    |   |-- national_population_hourly.parquet
    |   `-- national_tests_hourly.parquet
    |-- data_daily/
    |   |-- facility_cases_daily.csv
    |   |-- national_cases_daily.csv
    |   |-- national_population_daily.csv
    |   `-- national_tests_daily.csv
    `-- metadata/
        |-- facility_lookup.csv
        `-- parsing_metadata.csv
```

## `data_hourly/`
### `facility_cases_hourly.parquet`
Reported COVID-19 cases, by facility

|Variable                   |Type      |Description                            |
|:--------------------------|:---------|:--------------------------------------|
|page_downloaded            |`datetime`|The date and time when Vera archived the webpage |
|page_id                    |`string`  |A unique identifier for each archived page |
|page_md5                   |`string`  |The md5 checksum of the target page content, for deduplication|
|facility_listed            |`string`  |The facility name ICE listed on the archived webpage|
|facility_id                |`string`  |A unique identifier that Vera manually assigned to each listed facility|
|facility_name              |`string`  |The facility name standardized by Vera associated with each `facility_id`|
|facility_cases_cumulative  |`numeric` |The number ICE reported on the archived webpage for each detention facility as "total confirmed COVID-19 cases."|
|facility_cases_current     |`numeric` |Where available, the number ICE reported on the archived webpage for each detention facility as "confirmed cases currently under isolation or monitoring."|
|facility_deaths            |`numeric` |Where available, the number ICE reported on the archived webpage for each detention facility as "detainee deaths" (i.e., the total number of people per facility who died in ICE custody after testing positive for COVID-19).|
|ice_date_updated           |`datetime`|The italicized "Updated %mm/%dd/%yyyy" timestamp that appears below the page section corresponding to reports about people detained by ICE; for example, in the current version (V4), this timestamp is below the "COVID-19 ICE Detainee Statistics by Facility" table.|
|ice_date_as_of_case        |`datetime`|Where available, the "AS OF %m/%d/%yyyy" timestamp at the top of the "COVID-19 ICE Detainee Statistics by Facility" table on the archived webpage.|

### `./national_cases_hourly.parquet`
Reported COVID-19 cases, nationally

|Variable                   |Type      |Description                            |
|:--------------------------|:---------|:--------------------------------------|
|page_downloaded            |`datetime`|The date and time when Vera archived the webpage |
|page_id                    |`string`  |A unique identifier for each archived page |
|page_md5                   |`string`  |The md5 checksum of the target page content, for deduplication|
|cases_cumulative           |`numeric` |The *cumulative* number of confirmed COVID-19 cases ICE reported nationally.|
|cases_current_header       |`numeric` |Where available, the number of *current* COVID-19 cases reported nationally in the page header|
|cases_current_total_row    |`numeric` |Where available, the number of *current* COVID-19 cases reported nationally in the "TOTAL" row of the facility table|
|deaths_cumulative          |`numeric` |Where available, the number of *cumulative* COVID-19 deaths reported nationally in the "TOTAL" row of the facility table|
|ice_date_updated           |`datetime`|The italicized "Updated %mm/%dd/%yyyy" timestamp at the bottom-right of the case section|
|ice_date_as_of_case        |`datetime`|Where available, the "AS OF %m/%d/%yyyy" timestamp at the top of the facility case table|
|ice_date_as_of_case_current|`datetime`|Where available, the "AS OF %m/%d/%yyyy" timestamp found alongside the current case report in the page header|

### `./national_population_hourly.parquet`
The reported population of people detained by ICE

|Variable          |Type      |Description                                     |
|:-----------------|:---------|:-----------------------------------------------|
|page_downloaded   |`datetime`|The date and time when Vera archived the webpage |
|page_id           |`string`  |A unique identifier for each archived page |
|page_md5          |`string`  |The md5 checksum of the target page content, for deduplication|
|population_current|`numeric `|Where available, the reported population in ICE detention, nationally|
|ice_date_as_of_pop|`datetime`|Where available, the "AS OF" timestamp reported alongside national population detained reports|

### `./national_tests_hourly.parquet`
Reported COVID-19 tests administered, nationally

|Variable           |Type      |Description                                    |
|:------------------|:---------|:----------------------------------------------|
|page_downloaded    |`datetime`|The date and time when Vera archived the webpage |
|page_id            |`string`  |A unique identifier for each archived page |
|page_md5           |`string`  |The md5 checksum of the target page content, for deduplication|
|tests_cumulative   |`numeric` |Where available, the number of *total* COVID-19 tests administered nationally|
|ice_date_updated   |`datetime`|The italicized "Updated %mm/%dd/%yyyy" timestamp at the bottom-right of the case section|
|ice_date_as_of_test|`datetime`|Where available, the "AS OF" timestamp reported alongside test administration reports|


## `data_daily/`
These tables are subsets of the corresponding `data_hourly/` tables. Rather than including records for all archived pages for a given day, these tables retain only the records associated with the *latest* `page_downloaded` timestamp for a given day (corresponding to 0:00 to 23:59 Eastern Time).

### `./facility_cases_daily.csv`

|Variable                 |Type      |Description                               |
|:------------------------|:---------|:-----------------------------------------|
|facility_id              |`string`  |A unique identifier that Vera manually assigned to each listed facility|
|page_downloaded_day      |`date`    |The `page_downloaded` field, floored to the day |
|facility_cases_cumulative|`numeric` |The latest value assumed by the `facility_cases_cumulative` field in "_hourly" for this facility-day|
|facility_cases_current   |`numeric` |Where available, the latest value assumed by the `facility_cases_current` field in "_hourly" for this facility-day|
|facility_deaths          |`numeric` |Where available, the latest value assumed by the `facility_deaths` field in "_hourly" for this facility-day|
|facility_name            |`string`  |The facility name standardized by Vera associated with each `facility_id`|
|facility_listed          |`string`  |The latest value assumed by the `facility_listed` field in "_hourly" for a given `facility_id` on this day|
|ice_date_updated         |`datetime`|The `ice_date_updated` field, floored to the day|
|page_id                  |`string`  |The `page_id` for this record|

## `./national_cases_daily.csv`

|Variable            |Type      |Description                                        |
|:-------------------|:---------|:--------------------------------------------------|
|page_downloaded_day |`date`    |The `page_downloaded` field, floored to the day |
|cases_cumulative    |`numeric` |The latest value assumed by the `cases_cumulative` field in "_hourly" for this day|
|cases_current       |`numeric` |The latest value assumed by the max(`cases_current_total_row`, `cases_current_header`) field in "_hourly" for this day|
|deaths_cumulative   |`numeric` |The latest value assumed by the `deaths_cumulative` field in "_hourly" for this day|
|ice_date_updated    |`datetime`|The latest value assumed by the `ice_date_updated` field for a given `page_download_day`|
|page_id             |`string`  |The `page_id` for this record                      |

### `./national_population_daily.csv`

|Variable            |Type     |Description                                       |
|:-------------------|:--------|:-------------------------------------------------|
|page_downloaded_day |`date`   |The `page_downloaded` field, floored to the day |
|population_current  |`numeric`|Where available, the latest value assumed by the `tests` field in "_hourly" for this day|
|ice_date_as_of_pop  |`date`   |The latest value assumed by the `ice_date_as_of_pop` field for a given `page_download_day`|
|page_id             |`string` |The `page_id` for this record                     |

### `./national_tests_daily.csv`

|Variable           |Type      |Description                                      |
|:------------------|:---------|:------------------------------------------------|
|page_downloaded_day|`date`    |The `page_downloaded` field, floored to the day |
|tests_cumulative   |`numeric` |Where available, the latest value assumed by the `tests_cumulative` field in the "_hourly" table for this day|
|ice_date_as_of_test|`datetime`|The latest value assumed by the `ice_date_as_of_test` field for a given `page_download_day`|
|page_id            |`string`  |The `page_id` for this record                    |

## `metadata/`

### `./facility_lookup.csv`

|Variable     |Type     |Description                                            |
|:------------|:--------|:------------------------------------------------------|
|facility_listed|`string` |The facility name(s) as listed by ICE on the archived webpage|
|facility_id  |`string` |A unique identifier that Vera manually assigned to each listed facility|
|facility_name|`string` |The facility name standardized by Vera associated with each facility_id|
|lat          |`numeric`|Where available, the facility latitude, in degrees                     |
|lng          |`numeric`|Where available, the facility longitude, in degrees                    |
|state        |`string` |Where available, the facility state|
|lookup_source|`string` |Where available, the source of the facility standardization identifier|

### `./parsing_metadata.csv`
Page timestamps and other associated metadata

|Variable                   |Type      |Description                            |
|:--------------------------|:---------|:--------------------------------------|
|page_downloaded            |`datetime`|The date and time when Vera archived the webpage|
|page_id                    |`string`  |A unique identifier for each archived page |
|page_md5                   |`string`  |The md5 checksum of the target page content, for deduplication|
|ice_date_updated           |`datetime`|The italicized “Updated %mm/%dd/%yyyy” timestamp that appears below the “COVID-19 ICE Detainee Statistics by Facility” table on the archived webpage.|
|ice_date_as_of_case        |`datetime`|Where available, the “AS OF %m/%d/%yyyy” timestamp at the top of the “COVID-19 ICE Detainee Statistics by Facility” table on the archived webpage.|
|ice_date_as_of_case_current|`datetime`|Where available, the “AS OF %m/%d/%yyyy” timestamp found alongside the current case report in the page header.|
|ice_date_as_of_test        |`datetime`|Where available, the "AS OF" timestamp reported alongside test administration reports|
|ice_date_as_of_pop         |`datetime`|Where available, the "AS OF" timestamp reported alongside national population detained reports|
|ice_page_version           |`string`  |The page version; see [above](#ice-webpage-versions) for details|

# License
By downloading the data, you hereby agree to all the terms specified in this
[license](./License.md).

# Contributors
For questions or feedback about the data, please contact Noelle Smart at [nsmart@vera.org](mailto:nsmart@vera.org).

In acknowledgement of the effort put forth to assemble this resource and in an
effort to make this data available to all interested parties, please attribute
this data to "researchers from the Center on Immigration and Justice at the Vera
Institute of Justice" and [link](https://github.com/vera-institute/ice-detention-covid)
to this repository when using this resource. This work was a collaborative effort
involving Adam Garcia, Noelle Smart, Zachary Lawrence, and Dennis Kuo.
This data was first used in Vera's report ["The Hidden Curve: Estimating the Spread of COVID-19 among People in ICE Detention."](https://www.vera.org/the-hidden-curve-covid-19-in-ice-detention)

The Vera Institute of Justice is a non-profit organization that works to build and improve justice systems that ensure fairness, promote safety, and strengthen communities.
