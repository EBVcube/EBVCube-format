# EBVCube-format
This document defines the EBVCube format. This specification was developed at the [Biodiversity and Conservation](https://www.idiv.de/research/core-research-groups/biodiversity-conservation/) core group at iDiv: Luise Quoß <a href="https://orcid.org/0000-0002-9910-1252" target="_top"><img alt="ORCID iD" src="https://cran.r-project.org/web/orcid.svg" style="width:16px; height:16px; margin-left:4px; margin-right:4px; vertical-align:middle"></a>, 
Christian Langer <a href="https://orcid.org/0000-0003-1446-3527" target="_top"><img alt="ORCID iD" src="https://cran.r-project.org/web/orcid.svg" style="width:16px; height:16px; margin-left:4px; margin-right:4px; vertical-align:middle"></a>, 
Lina Estupinan Suarez <a href="https://orcid.org/0000-0002-2359-9496" target="_top"><img alt="ORCID iD" src="https://cran.r-project.org/web/orcid.svg" style="width:16px; height:16px; margin-left:4px; margin-right:4px; vertical-align:middle"></a>, 
Miguel Fernandez <a href="https://orcid.org/0000-0002-8301-1340" target="_top"><img alt="ORCID iD" src="https://cran.r-project.org/web/orcid.svg" style="width:16px; height:16px; margin-left:4px; margin-right:4px; vertical-align:middle"></a>, 
Andres Marmol <a href="https://orcid.org/0000-0001-9316-540X" target="_top"><img alt="ORCID iD" src="https://cran.r-project.org/web/orcid.svg" style="width:16px; height:16px; margin-left:4px; margin-right:4px; vertical-align:middle"></a>, 
Emmanuel Oceguera <a href="https://orcid.org/0009-0008-0107-9298" target="_top"><img alt="ORCID iD" src="https://cran.r-project.org/web/orcid.svg" style="width:16px; height:16px; margin-left:4px; margin-right:4px; vertical-align:middle"></a>, 
Jose Valdez  <a href="https://orcid.org/0000-0003-2690-9952" target="_top"><img alt="ORCID iD" src="https://cran.r-project.org/web/orcid.svg" style="width:16px; height:16px; margin-left:4px; margin-right:4px; vertical-align:middle"></a>, 
Nestor Fernandez  <a href="https://orcid.org/0000-0002-9645-8571" target="_top"><img alt="ORCID iD" src="https://cran.r-project.org/web/orcid.svg" style="width:16px; height:16px; margin-left:4px; margin-right:4px; vertical-align:middle"></a> and 
Henrique M. Pereira  <a href="https://orcid.org/0000-0003-1043-1675" target="_top"><img alt="ORCID iD" src="https://cran.r-project.org/web/orcid.svg" style="width:16px; height:16px; margin-left:4px; margin-right:4px; vertical-align:middle"></a>.

The files are based on the Network Common Data Form ([netCDF](https://www.unidata.ucar.edu/software/netcdf/documentation/4.7.4-pre/attribute_conventions.html)). Additionally, it follows the Climate and Forecast Conventions ([CF, version 1.8](https://cfconventions.org/Data/cf-conventions/cf-conventions-1.8/cf-conventions.html)) and the Attribute Convention for Data Discovery ([ACDD, version 1.3](https://wiki.esipfed.org/Attribute_Convention_for_Data_Discovery_1-3)). This data format complements the Essential Biodiversity Variables framework ([EBV](https://geobon.org/ebvs/what-are-ebvs/)).

## 1 Hierarchical data structure
### 1.1 Description
The EBVCube netCDF file structure supports multiple data cubes. These cubes have four dimensions: longitude, latitude, time and entity, whereby the last dimension can, e.g., encompass various biological or ecological categories, such as species, species groups, ecosystem types, or other groupings. Visualizing a **4D cube** can be a challenge. To facilitate its abstrasction, think of it as a data set where each pixel contains four pieces of information: spatial position (latitude and longitude), date (time), and the entity it represents (e.g., a species) (see [figure 1](#figure_dims)).

##### Figure 1: 4D data cube representation
<img name = figure_dims src="https://github.com/user-attachments/assets/bee955cc-c529-4d05-860b-7330b48bd64c" width="250">

The use of **hierarchical groups** allows multiple data cubes to coexist, with common dimensions across all cubes. The first hierarchical level (netCDF group) represents _scenarios_, such as various Shared Socioeconomic Pathways (SSP) scenarios used in modeling. The second hierarchical level (netCDF group) represents _metrics_, such as the percentage of protected area per pixel or the proportional loss over a certain time span per pixel. While the scenario-level is optional (no mandatory), each EBVCube netCDF must include at least one metric. If scenarios are included, all metrics must be repeated for each scenario (see [figure 2](#full_figure)). The number of scenarios and metrics included in the data sets can and will vary. 

The EBV data cubes (netCDF variables named 'ebv_cube') are defined as four dimensional arrays: longitude, latitude, time and entity. These **dimensions** are defined at the root level of the netCDF file, ensuring the consistency of the data cubes. The longitude and latitude spatial dimensions determinate the geographical extent and resolution of the data, while time dimension is the only unlimited dimension. This design enables the data sets to be updated over time by incorporing new temporal EBV monitoring information. Each of these three dimensions (longitude, latitude, and time) is accompanied by a corresponding coordinate variable at the root level, following the CF convention. The fourth dimension, the entity, can encompass a range of biological and ecological categories such as individual species, species groups, ecosystem types included in any EBV measurement. This information is stored in a character array named ‘entity’ at the root level and is referred to in CF terminology as an auxiliary coordinate variable. 

Summary: 
* Two possible nested sub-groups: scenario and metric
* Metric is a mandatory group, scenario is optional
* The scenario group is always higher than the metric
* If several metrics are present, they need to be repeated for all scenarios
* Hence several 4D data cubes (one per scenario-metric-path or metric-path) are possible
* The dimensions of the 4D data cubes are: longitude, latitude, time and entity

##### Figure 2: EBVCube hierarchical structure
<img name = full_figure src="https://github.com/user-attachments/assets/143c41df-afe8-4709-a35d-4f44bb377a12" width="600">

### 1.2 Example 1 (extensive) <a name='example1'></a> 
This is a schematic, rather technical representation of the netCDF structure of EBVCube data that incorporates the optional scenarios (note: more scenarios and/or metrics are possible). In contrast to the [figure above](#figure1) it covers all components of the netCDF including the dimensions, coordinate variables and georeferencing components. There are ATTRIBUTES at various levels and components. These are listed in the tables below in the [Metadata](#metadata) section.

If you have modeled your data for different scenarios, e.g. for the SSP scenarios, the [Global trends in biodiversity (BES-SIM PREDICTS)](https://portal.geobon.org/ebv-detail?id=28) data set by Samantha Hill is a good example to follow. FYI: this data set only has one entity: Alltaxa.

``` shell
┌── root level
├── GLOBAL ATTRIBUTES
├── Dimensions: entity, time, lat, lon, nchar
├── crs [0]
|   └── ATTRIBUTES
├── lat [lat]
|   └── ATTRIBUTES
├── lon [lon]
|   └── ATTRIBUTES
├── time [time]
|   └── ATTRIBUTES
├── entity [entity, nchar]
|   └── ATTRIBUTES
├── scenario_1
|   ├── ATTRIBUTES
│   ├── metric_1 
|   |   ├── ATTRIBUTES
|   |   └── ebv_cube [entity, time, lat, lon] 
|   |       └── ATTRIBUTES
|   └── metric_2
|       ├── ATTRIBUTES
|       └── ebv_cube [entity, time, lat, lon] 
|           └── ATTRIBUTES
└── scenario_2
|   ├── ATTRIBUTES
│   ├── metric_1 
|   |   ├── ATTRIBUTES
|   |   └── ebv_cube [entity, time, lat, lon] 
|   |       └── ATTRIBUTES
|   └── metric_2
|       ├── ATTRIBUTES
|       └── ebv_cube [entity, time, lat, lon] 
|           └── ATTRIBUTES
...
```

### 1.3 Example 2 (minimal)
The following representation follows the same style as [example 1](#example1) above. The difference is that this is the minimum EBVCube data  set you can create: no scenarios and only one metric. Of course, an EBVCube data set can also contain no scenarios, but several metrics. 

If your data set follows this or a similar structure, the [Habitat availability for African great apes](https://portal.geobon.org/ebv-detail?id=7) data set by Jessica Junker is a good example to follow. FYI: this data set has seven entities – one per great apes species.

``` bash
┌── root level
├── GLOBAL ATTRIBUTES
├── DIMENSIONS: entity, time, lat, lon, nchar
├── crs [0]
|   └── ATTRIBUTES
├── lat [lat]
|   └── ATTRIBUTES
├── lon [lon]
|   └── ATTRIBUTES
├── time [time]
|   └── ATTRIBUTES
├── entity [entity, nchar]
|   └── ATTRIBUTES
└── metric_1 
    ├── ATTRIBUTES
    └── ebv_cube [entity, time, lat, lon] 
        └── ATTRIBUTES

```

### 1.4 Example 3 (taxonomy) <a name='example3'></a> 
In contrast to the first two examples, this one includes the variables that are added to the netCDF when taxonomic information is given. For a detailed description see the [taxonomy section](#taxonomy). 

``` shell
┌── root level
├── GLOBAL ATTRIBUTES
├── Dimensions: entity, time, lat, lon, nchar, taxonlevel, nchar_taxonlist, nchar_taxonid
├── crs [0]
|   └── ATTRIBUTES
├── lat [lat]
|   └── ATTRIBUTES
├── lon [lon]
|   └── ATTRIBUTES
├── time [time]
|   └── ATTRIBUTES
├── entity [entity, nchar]
|   └── ATTRIBUTES
├── taxonomy_levels [nchar_taxonlist, taxonlevel]
|   └── ATTRIBUTES
├── taxonomy_table [nchar, entity, taxonlevel]
|   └── ATTRIBUTES
├── taxonomy_key [nchar_taxonid, entity]
|   └── ATTRIBUTES
└── metric_1 
    ├── ATTRIBUTES
    └── ebv_cube [entity, time, lat, lon] 
        └── ATTRIBUTES

```

<a name='metadata'></a> 
## 2 Metadata 
The following tables describe the attributes in the EBV netCDF files. Each table corresponds to a different component in the netCDF. The descriptions of the attributes that are derived from the ACDD, are directly cited from the [ACDD 1.3 documentation](https://wiki.esipfed.org/Attribute_Convention_for_Data_Discovery_1-3). The fifth column (User Input) marks all the attributes that need to be defined by the publisher at the upload form at the [EBV Data Portal](https://portal.geobon.org/). The sixth column (Mandatory) shows wether this input by the publisher is mandatory.   
Note: These attributes differ in part from those in the metadata files (XML, JSON) in the EBV Data Portal, as they also include the netCDF-specific, often more technical attributes. The [How-To](https://portal.geobon.org/downloads/pdf/how_to_ebv-portal.pdf) of the EBV Data Portal explains the terms of the upload form and maps them to the netCDF attributes described below.

### 2.1 Global attributes
The global [netCDF attributes](https://cfconventions.org/Data/cf-conventions/cf-conventions-1.8/cf-conventions.html#_attributes) are those that can be found at the root (global) level of the netCDF file. The table follows the order of the attributes in the JSON files of the EBV Data Portal. The additional terms are then listed.

|Level |Attribute|Comment|Convention|User Input|Mandatory|
| --- |  --- | --- | --- |--- | --- |
|Root |id | An identifier for the data set, provided by and unique within its naming authority. (Currently simple integer, currently preparing transfer to DOI) |ACDD|No|-|
|Root |naming_authority| The organization that provides the initial id for the dataset. Fixed value: 'The German Centre for Integrative Biodiversity Research (iDiv) Halle-Jena-Leipzig' |ACDD|No|-|
|Root| title|A short phrase or sentence describing the dataset.|ACDD, CF |Yes|Yes|
|Root |  date_created |The date on which this version of the data was created.| ACDD |Yes|Yes|
|Root |  date_issued |The date on which this data (including all modifications) was formally issued (i.e., made available to a wider audience) at the EBV Data Portal. |ACDD|No|-|
|Root |  date_modified |The date on which the data was last modified. Note that this applies just to the data, not the metadata. The ISO 8601:2004 extended date format is recommended. |ACDD|No|-|
|Root |  date_metadata_modified |The date on which the metadata was last modified. The ISO 8601:2004 extended date format is recommended. |ACDD|No|-|
|Root |  product_version |Version identifier (integer value) of the data file or product as assigned by the data creator. For example, a new algorithm or methodology could result in a new product_version. |ACDD|No|-|
|Root| summary|A paragraph describing the dataset, analogous to an abstract for a paper.|ACDD|Yes|Yes|
|Root |  references|Published or web-based references that describe the data or methods used to produce it.|ACDD, CF |Yes|No|
|Root |  source | The method of production of the original data. If it was model-generated, source should name the model and its version. If it is observational, source should characterize it. |ACDD, CF|Yes|Yes|
|Root |  project_name | The name of the project(s) principally responsible for originating this data. Multiple projects can be separated by commas.|EBV|Yes|No|
|Root |  project_url | The URL(s) of the project(s).|EBV|Yes|No|
|Root |  creator_name|The name of the person principally responsible for creating this data.| ACDD|Yes|Yes|
|Root |   creator_email |The email address of the person principally responsible for creating this data.|ACDD |Yes|No|
|Root |   creator_institution |The institution of the creator; should uniquely identify the creator's institution. |ACDD |Yes|Yes|
|Root |   contributor_name |The name of any individuals, projects, or institutions that contributed to the creation of this data.|ACDD|Yes|No|
|Root |   license |Provide the URL to a standard or specific license, enter "Freely Distributed" or "None", or describe any restrictions to data access and distribution in free text. |ACDD|Yes|Yes|
|Root |   publisher_name | The name of the person responsible for publishing the data file or product to users, with its current metadata and format. |ACDD|Yes|Yes|
|Root |   publisher_email |The email address of the person responsible for publishing the data file or product to users, with its current metadata and format. |ACDD|Yes|Yes|
|Root |   publisher_institution| The institution that presented the data file or equivalent product to users; should uniquely identify the institution.| ACDD|Yes|Yes|
|Root|	ebv_class|EBV Class of the dataset. |EBV|Yes|Yes|
|Root|	ebv_name|EBV Name of the dataset. |EBV|Yes|Yes|
|Root|  ebv_scenario_classification_name |Name of the applied scenario classification (if a scenario is used – not mandatory).|EBV|Yes|No|
|Root|  ebv_scenario_classification_version |Version of the scenario classification (if a scenario is used – not mandatory).|EBV|Yes|No|
|Root|  ebv_scenario_classification_url |URL of the scenario classification (if a scenario is used – not mandatory).|EBV|Yes|No|
|Root|  ebv_geospatial_scope |Spatial scope of the dataset, either ‘Continental/ Regional’, ‘National’, ‘Sub-national/Local’ or ‘Global’.|EBV|Yes|Yes|
|Root|  ebv_geospatial_description |Specific information about the spatial scope.|EBV|Yes|Yes|
| Root | geospatial_lat_resolution  | Information about the targeted spacing of points in latitude. Describes the resolution as a numeric value and its units. |ACDD|No|-|
| Root | geospatial_lon_resolution | Information about the targeted spacing of points in longitude. Describes the resolution as a numeric value and its units. |ACDD|No|-|
| Root | geospatial_lat_units | Units for the longitude axis. These are presumed to be ‘degrees_north’ or ‘meters_north’. |ACDD|No|-|
| Root | geospatial_lon_units | Units for the longitude axis. These are presumed to be ‘degrees_east’ or ‘meters_east’. |ACDD|No|-|
|Root| geospatial_bounds_crs|The coordinate reference system (CRS) of the point coordinates in the geospatial_bounds attribute. EPSG CRSs are strongly recommended. Example: 'EPSG:4326'|ACDD|No|-|
| Root | geospatial_bounds | Describes the data's 2D or 3D geospatial extent in OGC's Well-Known Text (WKT) Geometry format (reference the OGC Simple Feature Access (SFA) specification). Example: 'POLYGON ((40.26 -111.29, 41.26 -111.29, 41.26 -110.29, 40.26 -110.29, 40.26 -111.29))'|ACDD|No|-|
| Root | time_coverage_resolution | Describes the targeted time period between each value in the data set (ISO 8601:2004 date format). |ACDD|Yes|Yes|
| Root | time_coverage_start | Describes the time of the first data point in the data set (ISO 8601:2004 date format). |ACDD|Yes|Yes|
| Root | time_coverage_end | Describes the time of the last data point in the data set (ISO 8601:2004 date format). |ACDD|Yes|Yes|
|Root|  ebv_domain |Environmental domain of the dataset, one or several of ‘Terrestrial’, ‘Marine’ or ‘Freshwater’.|EBV|Yes|Yes|
|Root |   comment | Miscellaneous information about the data, not captured elsewhere.|CF, ACDD|Yes|No|
|Root | Conventions| A comma-separated list of the conventions that are followed by the dataset. Fixed value: 'CF-1.8, ACDD-1.3, EBV-1.0' |ACDD, CF|No|-|
|Root| keywords |A comma-separated list of key words and/or phrases.|ACDD|Yes|No|
|Root|  ebv_vocabulary | URL to controlled vocabulary for ebv_class and ebv_name. Fixed value: 'https://portal.geobon.org/api/v1/ebv' |EBV|No|-|
|Root|  ebv_cube_dimensions |Fixed value: 'lon, lat, time, entity' |EBV|No|-|
|Root |   history |Provides an audit trail for modifications to the original data. |ACDD, CF, netCDF Convention|No|-|

### 2.2 Scenario and metric attributes
The scenario and the metric are [netCDF groups](https://cfconventions.org/Data/cf-conventions/cf-conventions-1.8/cf-conventions.html#groups). They are nested. The scenario is the higher level, but unlike the metric, it is not mandatory. The metrics are repeated in all scenarios (if applicable). The following attribtues are found at the metric- and scenario-level. These attributes can also be found in the JSON files.

|Level |Attribute|Comment|Convention|User Input|Mandatory|
| --- |  --- | --- | --- |--- | --- |
|Metric	| standard_name	|Short group name|CF|Yes|Yes|
|Metric|	long_name| Extensive group name / description|CF|Yes|Yes|
|Metric|	units| The units of the metrics's data. |CF|Yes|Yes|
|Scenario|	standard_name|Short group name|CF|Yes|No|
|Scenario|	long_name|Extensive group name / description|CF|Yes|No|

### 2.3 Data cube attributes
The data cubes (ebv_cube) are [netCDF variables](https://cfconventions.org/Data/cf-conventions/cf-conventions-1.8/cf-conventions.html#variables). There is one data cube per (scenario-) metric-path in the netCDFs. Hence, multiple data cubes can be found in one EBVCube netCDF. Only the *coverage_content_type* attribute is present in the JSON files. The other attributes repeat metric-information and cover technical aspects. For example, the [*grid_mapping*](https://cfconventions.org/Data/cf-conventions/cf-conventions-1.8/cf-conventions.html#coordinate-system) attribute points to the variable in the netCDF that holds the coordiante reference related attributes (see section [Coordinate reference system attributes](#crs)). The *coordinates* attribute is pointing to the [auxiliary coordinate variable](https://cfconventions.org/Data/cf-conventions/cf-conventions-1.8/cf-conventions.html#labels)

|Level |Attribute|Comment|Convention|User Input|Mandatory|
| --- |  --- | --- | --- |--- | --- |
| ebv_cube | grid_mapping | Fixed value: '/crs' (Pointer to the coordinate reference system variable.) | CF|No|-|
| ebv_cube | long_name | Currently redundant to the standard_name of the corresponding metric. Will be updated in a future version. | CF, ACDD|Yes|Yes|
| ebv_cube | coordinates | Fixed value: '/entity' (Pointer to the coordinate variable holding the string values.) | CF|No|-|
| ebv_cube | units | Currently redundant the units of the corresponding metric. Will be updated in a future version. | CF, ACDD|Yes|Yes|
| ebv_cube | coverage_content_type | An ISO 19115-1 code to indicate the source of the data (image, thematicClassification, physicalMeasurement, auxiliaryInformation, qualityInformation, referenceInformation, modelResult, or coordinate). | ACDD|Yes|Yes|
| ebv_cube | _FillValue | internal netCDF attribute | netCDF Convention, CF |No|-|
| ebv_cube | _ChunkSizes | internal netCDF attribute | netCDF Convention |No|-|

### 2.4 Latitude and longitude attributes
The [lat](https://cfconventions.org/Data/cf-conventions/cf-conventions-1.8/cf-conventions.html#latitude-coordinate) and [lon](https://cfconventions.org/Data/cf-conventions/cf-conventions-1.8/cf-conventions.html#longitude-coordinate) are coordinate variables. The lon and lat dimensions are the basis for these two one-dimensional vectors, which contain the lon/lat values of the CRS of the data set. The lat and lon attributes follow the CF convention for [Horizontal Coordinate Reference Systems](https://cfconventions.org/Data/cf-conventions/cf-conventions-1.8/cf-conventions.html#grid-mappings-and-projections). These attributes cannot be found in the JSON files.

|Level |Attribute|Comment|Convention|User Input|Mandatory|
| --- |  --- | --- | --- |--- | --- |
| lon | axis |Fixed value: 'X' |CF|No|-|
| lon | units | 'degree_east' or 'meter' |CF|No|-|
| lon | standard_name | 'longitude' or 'projection_x_coordinate' |CF|No|-|
| lon | long_name | 'lon' |CF|No|-|
| lat | axis | Fixed value: 'Y' |CF|No|-|
| lat | units | 'degree_north' or 'meter' |CF|No|-|
| lat | standard_name | 'latitude' or 'projection_x_coordinate' |CF|No|-|
| lat | long_name | 'lat' |CF|No|-|

### 2.5 Temporal attributes
The [time](https://cfconventions.org/Data/cf-conventions/cf-conventions-1.8/cf-conventions.html#time-coordinate) is a coordinate variables. This one-dimensional vector is based on the time dimension. It holds the 'days since 1860' as integer values. These attributes cannot be found in the JSON files.

|Level |Attribute|Comment|Convention|User Input|Mandatory|
| --- |  --- | --- | --- |--- | --- |
| time | axis | Fixed value:  'T' | CF |No|-|
| time | calendar | Fixed value: 'standard' (Gregorian) | CF|No|-|
| time | units | Fixed value:  'days since 1860-01-01 00:00:00.0' | CF |No|-|
| time | long_name | Fixed value:  'time' | CF |No|-|
| time | _ChunkSizes |internal netCDF attribute | netCDF Convention|No|-|

### 2.6 Entity attributes
The entity variable is an [auxiliary coordinate variable](https://cfconventions.org/Data/cf-conventions/cf-conventions-1.8/cf-conventions.html#labels) and stores all entity names as a character array. The *ebv_entity_\** attributes are also included in the JSON files. 

|Level |Attribute|Comment|Convention|User Input|Mandatory|
| --- |  --- | --- | --- |--- | --- |
|entity| ebv_entity_type |EBV entity type, e.g., ‘Communities’.|EBV|Yes|Yes|
|entity| ebv_entity_scope |Specifies the entity scope in more detail, e.g., ‘Birds, Forest Birds, Non Forest Birds’|EBV|Yes|Yes|
|entity| ebv_entity_classification_name|Name of the classification system used for the entity types (optional). |EBV|Yes|No|
|entity| ebv_entity_classification_url |URL of the classification system used for the entity types (optional).|EBV|Yes|No|
|entity| units |Fixed value: '1' for 'unity' (udunits)|CF|No|-|
|entity| long_name |Fixed value: 'entity' |CF|No|-|

### 2.7 Coordinate reference system attributes <a name='crs'></a> 
All attributes regarding the georeferencing can be found at the 'crs' variable in the EBVCube netCDFs. The georeferencing is following the [grip mappings](https://cfconventions.org/Data/cf-conventions/cf-conventions-1.8/cf-conventions.html#grid-mappings-and-projections) by the CF convention. Therefore the attributes differ based on the coordinate reference system. Read the [CF convention section](https://cfconventions.org/Data/cf-conventions/cf-conventions-1.8/cf-conventions.html#appendix-grid-mappings) for more information. Additionally, the *GeoTransform* and *spatial_ref* attributes are added based on the netCDF definitions by [GDAL](https://gdal.org/en/stable/drivers/raster/netcdf.html#georeference). These attributes cannot be found in the JSON files.

FYI: In principle, you can assign all CRSs available in the PROJ library to an EBVCube netCDF. The only restriction is currently the visualization in the map of the EBV Data Portal by the company GeoEngine, which only works for EPSG-based CRSs.  

|Level |Attribute|Comment|Convention|User Input|Mandatory|
| --- |  --- | --- | --- |--- | --- |
| crs | grid_mapping_name | String value that contains the mapping’s name, e.g., WGS84 has the value 'latitude_longitude'.  | CF |No|-|
| crs | * | Attributes that define a specific mapping depend on the value of ‘grid_mapping_name’ (FGDC "Content Standard for Digital Geospatial Metadata"), e.g., for WGS84: ‘longitude_of_prime_meridian’, ‘semi_major_axis’ and ‘inverse_flattening’.| CF|No|-|
| crs | spatial_ref |  WKT2 representation of CRS |GDAL|No|-|
| crs | GeoTransform |  GeoTransform array: 'x_ul x_res x_rotation y_ul y_rotation y_res' |GDAL|No|-|
| crs | long_name | Fixed value: 'CRS definition'|CF| No|-|
 
### 2.8 Taxonomy attributes
If the dataset encompasses taxonomic information at least two new variables are added. The taxonomy_key variable is only added if the user also provides the taxonomic keys. 
If an EBVCube netCDF holds taxonomic information, the ebv_entity_classification_name and ebv_entity_classification_url, associated to the entity variable, point to the taxonomic backbone and its URL. 

|Level |Attribute|Comment|Convention|User Input|Mandatory|
| --- |  --- | --- | --- |--- | --- |
|taxonomy_levels| units |Fixed value: '1' for 'unity' (udunits)|CF|No|-|
|taxonomy_table| units |Fixed value: '1' for 'unity' (udunits)|CF|No|-|
|taxonomy_key| units |Fixed value: '1' for 'unity' (udunits)|CF|No|-|
|taxonomy_key| long_name |The name of the taxonomic key from the authority of the taxonomic backbone. For example, if your taxonomy is based on the [GBIF Backbone Taxonomy](https://www.gbif.org/dataset/d7dddbf4-2cf0-4f39-9b2a-bb099caae36c) the value could be "usageKey".|EBV|Yes|Yes|


<a name='taxonomy'></a> 
## 3. Taxonomy

### 3.1 Introducation 
If the entities in your dataset follow a taxonomy, you can also add this information to an EBVCube dataset. The taxonomy can cover species as well as habitat types and more. 

For example the [Occurrence Metrics for Invasive Alien Species of Union Concern in EU27: A 10 km prototype using GBIF occurrence cubes ](https://portal.geobon.org/ebv-detail?id=83) dataset hold species data following the GBIF taxonomic backbone. The taxonomic information is directly added during the creation of the netCDF file. You can follow the code [here](https://doi.org/10.5281/zenodo.13798783).  

The ebvcube R package retrieves the taxonomic information for you (see below), and it is also displayed per dataset on the EBV Data Portal detail pages. Further developments are currently underway. This encompasses for example the display of the taxonomy in the [EBVCubeVisualizer](https://github.com/EBVcube/EBVCubeVisualizer) and the integration into the EBV Data Portal API.

### 3.2 Technical representation
To store the taxonomic information two netCDF variables (character arrays) are added to the netCDF. The 'entity_taxonomy_levels' and the 'entity_taxonomy_table'. 

The 'entity_taxonomy_levels' is a 2D array (dimensions: nchar_taxonlist, taxonlevel) that holds the names of the different taxonomy levels, e.g. 'species', 'genus', 'family', 'order', 'class', 'phylum' and 'kingdom'.
The 'entity_taxonomy_table' is a 3D array (dimensions: nchar, entity, taxonlevel) that holds the values of all taxonomy levels per entity, e.g. for one entity 'Accipiter brevipes', 'Accipiter', 'Accipitridae', 'Accipitriformes', 'Aves', 'Chordata' and 'Animalia'.
A schematic representation can be found in [example 3](#example3).

FYI: these variables were called 'entity_levels' and 'entity_list' until R package version 0.4.0. The renaming was done in the beginning of April in 2025. The later R package version are able to handle both namings. 

### 3.3 Read taxonomy with R and Python
To get the taxonomic information with R run the following code:
``` r

#import packages
library(ebvcube)

#download the EBVCube file
dir <- tempdir()
filepath <- ebv_download(id = 83,
                         outputdir = dir)

#read properties
prop <- ebv_properties(filepath, verbose=F)

#get the taxonomy
taxonomy <- prop@general$taxonomy
  
#print the first line
print(taxonomy[1,])
#         species  genus   family   order         class       phylum kingdom
#1 Acacia saligna Acacia Fabaceae Fabales Magnoliopsida Tracheophyta Plantae

```

To get the taxonomic information with Python run the following code:
```python

#import packages
import netCDF4 as nc
import numpy as np
import tempfile
import wget

#download the EBVCube file
url = 'https://portal.geobon.org/data/upload/82/public/suarez_spepop_id82_20240820_v1.nc'
temp_dir = tempfile.TemporaryDirectory()
filepath = wget.download(url, out = temp_dir.name)

#open netCDF file read-only
rootgrp = nc.Dataset(filepath,"r")

#get the taxonomy levels
tax_level = np.transpose(np.array(rootgrp['entity_levels'])) #new naming: taxonomy_levels
tax_level_list = [tax_level[i,:].tobytes().decode('UTF-8').strip() for i in range(tax_level.shape[0])]
#print the taxon levels
print(tax_level_list)
# Out: ['species', 'genus', 'family', 'order', 'class', 'phylum', 'kingdom']

#get the taxonomy list data and transform it into a dictionary
tax_list = np.transpose(np.array(rootgrp['entity_list'])) #new naming: taxonomy_table
#collect taxonomy of this dataset
result_tax = dict()
result_tax['entity_id'] = tax_level_list
for entity in range(tax_list.shape[1]):
    row = [tax_list[:,entity,:][i,:].tobytes().decode('UTF-8').strip() for i in range(tax_list[:,entity,:].shape[0])]
    result_tax[(entity+1)] = row
#print the first row
print(result_tax[1])
# Out: ['Acacia saligna', 'Acacia', 'Fabaceae', 'Fabales', 'Magnoliopsida', 'Tracheophyta', 'Plantae']

#close netCDF file
rootgrp.close()

```

## 4. Tools 
### 4.1 Exploring EBVCubes
To discover the EBV netCDFs in full detail, we recommend [Panoply](https://www.giss.nasa.gov/tools/panoply/). This is a software developed by NASA to generally open HDF5/netCDF files. This tool allows you to see all components of the netCDF including the internal hierarchy and all attribues. Besides, it has a plot function. This can be a bit overwhelming as you also see all the distributed technical components and attributes. But it is a very nice way to deeply understand the EBV netCDFs without code. 

You can also download and open the EBVCube netCDFs directly in your R code with the [ebvcube R package](https://github.com/EBVcube/ebvcube). This package bundles all the important metadata for you and hides all ‘unnecessary’ technical stuff. Also, you can directly start working with the data. It provides some easy high-level functions to, e.g., directly visualize or subset the data as you like. As the data slices are (also) available as [terra](https://cran.r-project.org/web/packages/terra/index.html) SpatRasters, you can include the ebvcube code snippets in any geospatial analysis you are performing with the terra tools. 

In addition, we have developed a QGIS plugin called [EBVCubeVisualizer](https://github.com/EBVcube/EBVCubeVisualizer). This plugin allows the user to explore the full metadata and hierarchical structure of EBVCube datasets, similar to the Panoply software - but directly in QGIS. It strikes a good balance between displaying the full structure and metadata while hiding technical components and attributes (in contrast to Panoply) for improved user-friendliness. It allows users to extract and visualize specific slices from EBV data cubes, with flexible selection by time, entity, scenario and metric. As with all other layers in QGIS, you can use all geospatial tools directly. 

### 4.2 Creating EBVCubes
The creation of the EBVCube netCDF is supported via the ebvcube R package. You can find the most recent development version [here](https://github.com/EBVcube/ebvcube/tree/dev) on GitHub. The package is also published on [CRAN](https://cran.r-project.org/web/packages/ebvcube/). The [Readme]([https://github.com/EBVcube/ebvcube/blob/dev/README.md](https://github.com/EBVcube/ebvcube/blob/main/README.md#34-take-a-peek-on-the-creation-of-an-ebv-netcdf)) of the GitHub repository explains shortly the workflow for the creation. The [How-To](https://portal.geobon.org/downloads/pdf/how_to_ebv-portal.pdf) on the EBV Data Portal has a section ('8. Training resources') summarizing different ressources including code examples for the creation of EBVCube netCDFs.

