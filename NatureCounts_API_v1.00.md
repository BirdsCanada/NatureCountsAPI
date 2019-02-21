# NatureCounts API Version 1.0 #

The goal of the API is to make NatureCounts data available to external client software and services.
It aims at replacing some of the R functions that were developed many years ago and require at direct ODBC connection to the database,
which is protected by a firewall.

The entrypoints described below will return a HTTP response status code as 

## Error Handling ##

If the server encounters an error processing a request, it will return a JSON Object with 3 attributes:

- "error": an [http response code](https://en.wikipedia.org/wiki/List_of_HTTP_status_codes)
- "entryPoint": a text representation of the entry point that generated the error
- "error_msg": a short text explanation


## MetaData Entry Points ##

These entry points do not require authentication. Their purpose is to return descriptive information that
will be helpful in querying collection data, or interpretting the same.

The metadata is returned as a JSON object, whose attributes are vectors of data values, named for their column heading. 


### BMDE Versions ###

Returns a list of the BMDE standards versions available (e.g. BMDE2.00-ext is the extensive version containing all available fields,
and BMDE-MKN-2.00 is a version designed for Monarch datasets. 

>**Example URL:** /api/metadata/bmde_versions


## BMDE Fields ##

Get the list of field names associated with a particular BMDE version.

Required parameter: **version** - a version obtained from the previous call

>**Example URL:** /api/metadata/bmde_fields?version=BMDE2.00


## Projects ##

Returns a list of NatureCounts projects, with their id, name and project url.

Optional parameter: **lang** - the language preference [EN|FR], default EN

>**Example URL:** /api/metadata/projects?lang=EN

## Projects Metadata ##

Returns a more comprehensive list of project metadata. The list of fields included remains to be defined,
but will likely at the very least include the terms and conditions and suggested citation statement.

Optional parameter: **lang** - the language preference [EN|FR], default EN

>**Example URL:** /api/metadata/projects_metadata?lang=EN


## Collections ##

Get a list of all available collections (datasets) in NatureCounts

Optional parameter: **lang** - the language preference [EN|FR], default EN

>**Example URL:** /api/metadata/collections?lang=EN


## Species ##

This query returns the list of species concepts recognized by NatureCounts. NatureCounts follows
Clements taxonomy for birds, but also support other taxa (birds and other groups). 

>**Example URL:** /api/metadata/species



## Species Codes Authority ##

Returns a list of all species codes authorities recognized in NatureCounts

>**Example URL:** /api/metadata/species_codes_authority

## Species Codes ##

Returns a list of all species codes recognized in NatureCounts, for all authorities, or only a specific one.

Optional parameter: **authority** - the	authority to use resolving codes

>**Example URL:** /api/metadata/species_codes?authority=BSCDATA


## Country ##

Returns a list of all country codes recognized in NatureCounts and for which there are records.

>**Example URL:** /api/metadata/country

## State / Province ##

Returns a list of all state and province codes recognized in NatureCounts and for which there are records.

>**Example URL:** /api/metadata/statprov

## Subnational Codes ##

Returns a list of all subnational2 codes (e.g. counties) recognized in NatureCounts and for which there are records. 

>**Example URL:** /api/metadata/subnat2

## BMDE Data Entry Points ##

Data access is controlled at the level of collections, where access levels are set. Visit
[a primer on data access](https://www.birdscanada.org/birdmon/default/nc_access_levels.jsp).


### Authentication ##

The API controls collection data access using a token that is generated when a user authenticates.
The token has a limited life span (currently 20 days), so the client application should require user authentication 
for each new session.

A user may hold multiple valid tokens at a time, allowing them to work from more than one session (or device) at once.

This entrypoint requires a login username and password, and if valid, return a JSON object carrying
a token with a 20 day validity period. The client application can then present this
token as user credentials in the data access entry points, where required / desired.


>**Example URL:** /api/data/authenticate?username=asdfasdf&password=qwerty

>**Example Response:** {"token":"1234567890qwerty"}

Note that errors result in a response package as described above.


## Filtering Data ##


BMDE Data requests support various fitering parameters, described here. Filters are submitted as a JSON object
on the request, along with the tokne (if required), as shown here:

>**Example Data Request with filter**: /api/data/get_data?token=qwertyqwerty&filter={ }

A sample filter structure is shown below.

In addition, the responses to raw data requests are paginated: the client application must support this as described below.

Filters are divided into two categories: Basic and Advanced. The Advanced filters are only applicable to the raw data request
(get_data).

### Basic Filtering ###

The following optional filter parameters can be submitted to most of the BMDE Data entrypoints. All must be encoded as JSON Arrays (vectors), as
shown in the example column.

| Parameter Name | Explanation | Example |
| -------------- | ----------- | ------- |
| **collection** | vector of codes used to filter the data based on collection codes | ["ABATLAS1","ABATLAS2","BBS50"] |
| **species** | vector of species id's used to filter the data based on species ID | [440,2700] |
| **country** | vector of country ISO codes | ["CA","US"] |
| **statprov** | vector of state or province ISO codes | ["ON","AB"] |
| **bcr** | vector or Bird conservation regions numbers | ["BCR.14"] |
| **iba** | vector of IBA site codes | ["IBA.SK016","IBA.AB112","IBA.AB115"] |
| **subnat2** | vector of subnat2 (e.g. county) codes | ["CA.ON.FR"] |

### Advanced Filtering ###

The following filters may not apply to all entrypoints:

| Parameter Name | Explanation | Example |
| -------------- | ----------- | ------- |
| **siteCode** | vector of codes used to filter the data based on site codes |
| **utm_square** | vector of 7-letter UTM atlas squares | ["17TNH42"] |
| **min_lat** | minimum latitude (decimal degrees) | 45.0000 |
| **max_lat** | maximum latitude (decimal degrees) | 47.4567 |
| **min_long** | minimum latitude (decimal degrees) | -87.9987 |
| **max_long** | minimum latitude (decimal degrees) | -87.4565 |
| **start_year** | earliest year | 2012 |
| **end_year** | final year | 2013 |
| **start_day** | earliest day in year (1-366) | 300 |
| **end_day** | final day in year (1-366) | 350 |

