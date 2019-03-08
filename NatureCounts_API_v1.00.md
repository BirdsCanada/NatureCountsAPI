# NatureCounts API Version 1.0 #

The goal of the API is to make NatureCounts data available to external client software and services.
It aims at replacing some of the R functions that were developed many years ago and require at direct ODBC connection to the database,
which is protected by a firewall.

### Table of Contents ###

1. MetaData Entry Points
2. [Data Exploration](#bmde-data-exploration)
	1. Data Filtering
	2. Data Entry Points

The entrypoints described below will return a HTTP response status code 200 on success. In the event of an error the HTTP
response code will reflect this, and the response payload will be a JSON Object with 3 attributes:

- **error:** an [http response code](https://en.wikipedia.org/wiki/List_of_HTTP_status_codes) matching the one in the response header
- **entryPoint:** a text representation of the entry point that generated the error
- **error_msg:** a short text explanation


## MetaData Entry Points ##

These entry points do not require authentication. Their purpose is to return descriptive information that
will be helpful in querying collection data, or interpretting the same.

The response payload is a JSON object, whose attributes are vectors of data values, named for their column heading. 


### API Version ###

`/api_version`

Returns JSON response with attribute 'api_version', equals to the current api version (String). This same
version string is returned as part of the response to a successful authentication request.

>**Example URL:** /api/api_version


### BMDE Versions ###

`/metadata/bmde_versions`

Returns a list of the BMDE standards versions available (e.g. BMDE2.00-ext is the extensive version containing all available fields,
and BMDE-MKN-2.00 is a version designed for Monarch datasets). 

>**Example URL:** /api/metadata/bmde_versions


### BMDE Fields ###

`/metadata/bmde_fields`

Get the list of field names associated with a particular BMDE version.

Required parameter: **version** - a version obtained from the previous call

>**Example URL:** /api/metadata/bmde_fields?version=BMDE2.00


### Projects ###

`/metadata/projects`

Returns a list of NatureCounts projects, with their id, name and project url.

Optional parameter: **lang** - the language preference [EN|FR], default EN

>**Example URL:** /api/metadata/projects?lang=EN


### Projects Metadata ###

`/metadata/projects_metadata`

Returns a more comprehensive list of project metadata. The list of fields included remains to be defined,
but will likely at the very least include the terms and conditions and suggested citation statement.

Optional parameter: **lang** - the language preference [EN|FR], default EN

>**Example URL:** /api/metadata/projects_metadata?lang=EN


### Collections ###

`/metadata/collections`

Get a list of all available collections (datasets) in NatureCounts

Optional parameter: **lang** - the language preference [EN|FR], default EN

>**Example URL:** /api/metadata/collections?lang=EN


### Species ###

`/metadata/species`

This query returns the list of species concepts recognized by NatureCounts. NatureCounts follows
Clements taxonomy for birds, but also support other taxa (birds and other groups). 

>**Example URL:** /api/metadata/species


### Species Codes Authority ###

`/metadata/species_code_authority`

Returns a list of all species codes authorities recognized in NatureCounts

>**Example URL:** /api/metadata/species_codes_authority


### Species Codes ###

`/metadata/species_codes`

Returns a list of all species codes recognized in NatureCounts, for all authorities, or only a specific one.

Optional parameter: **authority** - the	authority to use resolving codes

>**Example URL:** /api/metadata/species_codes?authority=BSCDATA


### Country ###

`/metadata/country`

Returns a list of all country codes recognized in NatureCounts and for which there are records.

>**Example URL:** /api/metadata/country


### State / Province ###

`/metadata/statprov`

Returns a list of all state and province codes recognized in NatureCounts and for which there are records.

>**Example URL:** /api/metadata/statprov


### Subnational Codes ###

`/metadata/subnat2`

Returns a list of all subnational2 codes (e.g. counties) recognized in NatureCounts and for which there are records. 

>**Example URL:** /api/metadata/subnat2


### Breeding Codes ###

`/metadata/breeding_codes`

Returns a list of all breeding codes.

>**Example URL:** /api/metadata/breeding_codes


### Protocols ###

`/metadata/protocols`

Returns a list of all protocols.

>**Example URL:** /api/metadata/protocols


### Protocol Types ###

`/metadata/protocol_type`

Returns a list of all protocol types.

>**Example URL:** /api/metadata/protocol_type


### Bird Conservation Regions ###

`/metadata/bcr`

Returns a list of all BCR's.

>**Example URL:** /api/metadata/bcr


### Important Bird Areas ###

`/metadata/iba_sites`

Returns a list of all IBA Sites.

>**Example URL:** /api/metadata/iba_sites


----

## BMDE Data Exploration ##

Data access is controlled at the level of collections. Read
[a primer on data access](https://www.birdscanada.org/birdmon/default/nc_access_levels.jsp) for full details.

The (successful) data query response payload will be a JSON Object with an attribute called `results`, carrying the data vectors. The
response structure may also include other attributes, mentioned below.



#### Authentication ####

The API controls collection data access using a token that is generated when a user authenticates.
The token has a limited life span (currently 20 days), so the client application should require user authentication 
for each new session.

A user may hold multiple valid tokens at a time, allowing them to work from more than one session (or device) at once.

This entrypoint requires a login username and password, and if valid, returns a JSON object carrying
a token with a 20 day validity period, as well as the current api_version designation (String).
The client application can then present the token as user credentials in the data access entry points, where applicable.

Required parameter: **username** - the account username

Required parameter: **password** - the account password

>**Example URL:** /api/data/authenticate?username=asdfasdf&password=qwerty

>**Example Response:** {"token":"1234567890qwerty"}



### Filtering Data ###


BMDE Data requests support filtering, as described in each entry point explanation below. Filters are submitted as a JSON object
on the request, along with the token (if required); an typical request will look like this:

>**Example Data Request with filter**: /api/data/get_data?token=qwertyqwerty&filter={ ... }

Details on the filter structure are found below.


#### Basic Filtering ####

The following optional filter parameters can be submitted to most of the BMDE Data entrypoints. All must be encoded as JSON Arrays (vectors), as
shown in the example column.

| Parameter Name | Type | Explanation | Example |
| -------------- | ---- | ----------- | ------- |
| **collections** | Vector of strings | Collection codes | ["ABATLAS1","ABATLAS2","BBS50"] |
| **species** | Vector of integers | Species id's | [440,2700] |
| **country** | Vector of strings | Country ISO codes | ["CA","US"] |
| **statProv** | Vector of strings | State or Province ISO codes | ["ON","AB"] |
| **bcr** | Vector of strings | Bird conservation regions numbers | ["BCR.14"] |
| **iba** | Vector of strings | IBA site codes | ["IBA.SK016","IBA.AB112","IBA.AB115"] |
| **subNat2** | Vector of strings | Subnational2 (e.g. county) codes | ["CA.ON.FR"] |


#### Advanced Filtering ####

The following filters may not apply to all entrypoints:

| Parameter Name | Type | Explanation | Example |
| -------------- | ---- | ----------- | ------- |
| **siteCode** | Vector of strings | Site codes | |
| **utmSquare** | Vector of stings | 7-letter UTM atlas squares | ["17TNH42"] |
| **minLat** | Real number | Minimum latitude (decimal degrees) | 45.0000 |
| **maxLat** | Real number | Maximum latitude (decimal degrees) | 47.4567 |
| **minLong** | Real number | Minimum latitude (decimal degrees) | -87.9987 |
| **maxLong** | Real number | Minimum latitude (decimal degrees) | -87.4565 |
| **startYear** | Integer | Earliest year | 2012 |
| **endYear** | Integer | Final year | 2013 |
| **startDay** | Integer | Earliest day in year (1-366) | 300 |
| **endDay** | Integer | Final day in year (1-366) | 350 |

See the Raw data entrypoint below for additional filtering applicable to that function only.

----------

## BMDE Data Entry Points ##

### List Permissions on Collections ###

`/data/list_permissions`

Returns a list of accessible data collections and permissions currently granted for each. If no token is supplied, the result list
will show only those collections that are Level 5 (public). If a token is provided, the list will also include 
collections to which the user has explicit access.

>Optional parameter: **token** - the session token to identify the user

>**Example URL:** /api/data/list_permissions?token=qwertyqwerty


### List Collections ###

`/data/list_collections`

Obtain a list of collection codes and some statistics about the number of records accessible,
matching the filter criteria, for one or more dataset of AKN level 2 or more.

Authentication not required.

>**Example URL:** /api/data/list_collections?filter={ ... }


### List Species ###

`/data/list_species`

Obtain a list of species ID’s and some statistics about the number of records accessible, matching the search criteria,
for any dataset of AKN level 2 or more.

Authentication not required.

>**Example URL:** /api/data/list_species?filter={ ... }


### Get Raw Data ###

`/data/get_data`

Obtain raw data for a given collection. This query requires a single collection be specified as the `collection` attribute in the filter parameter, and the collection
must be either public, or accessible via the user's token (see the `list_permissions` entry point above).

The specific filter attributes for this call:
 
| Parameter Name | Type | Explanation | Example |
| -------------- | ---- | ----------- | ------- |
| **collection** | String | A single collection code | "ABATLAS1" |
| **fields** | Vector of strings | Field names, if a subset of the standard fields is desired | ["ScientificName","InstitutionCode"] |
| **bmdeVersion** | String | **Not supported yet** | "BMDE2.00" |
| **ncFields** | Boolean | **Not supported yet** | true |


Authentication not required.

>Required filter attribute: **collection** - a specific collection code

>Optional parameter: **token** - required when accessing non-public collections

>Optional parameter: **lastRecord** - the highest `record_id` that the client received, defaulting to -1

>Optional parameter: **numRecords** - the number of records to return, subject to an upper limit

>Optional parameter: **requestId** - a request Id as a substitute for a filter structure (see below)

>**Example URL:** /api/data/raw_data?token=asdfasdf&filter={ ... }&lastRecord=0&numRecords=1000

The response payload will carry at least the `results` attribute.

The client application must treat this as a paginated call and expect to repeat the query multiple times, with updated `lastRecord` values on each subsequent request.
The client **must** expect more data if the result set was not empty and either:

1. The number of records in the result set was equal to the query value of `numRecords` (if used);
2. Or the response payload included a `requestId` attribute (see below);
 
In either of these cases, the client should expect another page of data, and should submit another request, with an updated `lastRecord` parameter.

Note that subsequent requests can use the `requestId` parameter as a substitute for the `filter` parameter. While not obligatory, this
strategy is recommended for efficiency. An example query of this type would look like this:

>**Example URL:** /api/data/raw_data?token=asdfasdf&requestId=1g2h3j4k5l6&lastRecord=13456&numRecords=1000

Once a result set has been returned that is empty or smaller than the `numRecords` requested, the `requestId` will become invalid, since
this event signals that there are no more pages of data associated with the original filter set.
