# EBVCube-format
This document defines the EBVCube format. This specification was developed at the [Biodiversity and Conservation](https://www.idiv.de/en/groups-and-people/core-groups/biodiversity-conservation.html) core group at iDiv. ADD CONTRIBUTORS

The files are based on the Network Common Data Form ([netCDF](https://www.unidata.ucar.edu/software/netcdf/documentation/4.7.4-pre/attribute_conventions.html)). Additionally, it follows the Climate and Forecast Conventions ([CF, version 1.8](https://cfconventions.org/Data/cf-conventions/cf-conventions-1.8/cf-conventions.html)) and the Attribute Convention for Data Discovery ([ACDD, version 1.3](https://wiki.esipfed.org/Attribute_Convention_for_Data_Discovery_1-3)). This data format complements the Essential Biodiversity Variables framework ([EBV](https://geobon.org/ebvs/what-are-ebvs/)).

## 1 Internal hierarchy
### 1.1 Description
The structure allows several data cubes per EBVCube netCDF file. These cubes have four dimensions: longitude, latitude, time and entity, whereby the last dimension can, e.g., encompass different species or groups of species, ecosystem types or other. Each cube holds data of a specific metric. 

The usage of hierarchical groups enables the coexistence of multiple data cubes, each sharing the same dimensions. The first hierarchical level (netCDF group) are scenarios, e.g., the modelling for different Shared Socioeconomic Pathways (SSP) scenarios. The second hierarchical level (netCDF group) represent metrics, such as the percentage of protected area per pixel or the proportional loss over a certain time span per pixel. In contrast to the scenario-level (which is not mandatory), each EBVCube netCDF must have at least one metric. All metrics are repeated per scenario, if any are present. The number of scenarios and metrics included in the data sets can and will vary. 

The EBV data cubes (netCDF variables named 'ebv_cube') are defined as four dimensional arrays: longitude, latitude, time and biological entity. These dimensions are defined at the root level of the netCDF file. Longitude and latitude are spatial dimensions that determine the geographical extent and resolution of the data. The time dimension is the only unlimited dimension. This allows updating the datasets adding new temporal EBV monitoring information. For each of these three dimensions a coordinate variable is added to the root level following the CF Convention. The fourth dimension, the biological entity, can encompass different species or groups of species, ecosystem types or other biological categories contained in any EBV measurement. This information is stored in a character array named ‘entity’ at the root level. In the CF terminology this is called an auxiliary coordinate variable. 

Summary: 
* Two possible nested sub-groups: scenario and metric
* Metric is a mandatory group, scenario is optional
* The scenario group is always higher than the metric
* If several metrics are present, they need to be repeated for all scenarios
* Hence several 4D data cubes (one per scenario-metric-path or metric-path) are possible
* The dimensions of the 4D data cubes are: longitude, latitude, time and entity

![vis_4d](https://github.com/user-attachments/assets/760e7d11-3370-429c-8371-532b66dbc5ee)
### 1.2 Example 1 (extensive)
* Note: there would be more scenarios and/or metrics possible
``` bash
┌── root level
├── GLOBAL ATTRIBUTES
├── DIMENSIONS: entity, time, lat, lon
├── crs [0]
|   └── ATTRIBUTES
├── lat [lat]
|   └── ATTRIBUTES
├── lon [lon]
|   └── ATTRIBUTES
├── time [time]
|   └── ATTRIBUTES
├── entity [entity]
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
``` bash
┌── root level
├── GLOBAL ATTRIBUTES
├── DIMENSIONS: entity, time, lat, lon
├── crs [0]
|   └── ATTRIBUTES
├── lat [lat]
|   └── ATTRIBUTES
├── lon [lon]
|   └── ATTRIBUTES
├── time [time]
|   └── ATTRIBUTES
├── entity [entity]
|   └── ATTRIBUTES
└── metric_1 
    ├── ATTRIBUTES
    └── ebv_cube [entity, time, lat, lon] 
        └── ATTRIBUTES

```

## 2 Metadata
The following tables describe the attributes of the EBV netCDF files. Each table corresponds to a different component in the netCDF. The descriptions of the attributes that are derived from the ACDD, are directly cited from the [ACDD 1.3 documentation](https://wiki.esipfed.org/Attribute_Convention_for_Data_Discovery_1-3). The fifth column (User input) marks all the attributes that need to be defined by the publisher. The sixth column (Mandatory) shows wether this input by the publisher is mandatory.

### 2.1 General Attributes
|Level |Attribute|Comment|Convention|User Input|Mandatory|
| --- |  --- | --- | --- |--- | --- |
|Root |id | An identifier for the data set, provided by and unique within its naming authority. (Currently simple integer, currently preparing transfer to DOI) |ACDD|No|-|
|Root |  naming_authority| The organization that provides the initial id for the dataset. Fixed value: 'The German Centre for Integrative Biodiversity Research (iDiv) Halle-Jena-Leipzig' |ACDD|No|-|
|Root | Conventions| A comma-separated list of the conventions that are followed by the dataset. Fixed value: 'CF-1.8, ACDD-1.3, EBV-1.0' |ACDD, CF|No|-|
|Root| title|A short phrase or sentence describing the dataset.|ACDD, CF |Yes|Yes|
|Root| summary|A paragraph describing the dataset, analogous to an abstract for a paper.|ACDD|Yes|Yes|
|Root |  references|Published or web-based references that describe the data or methods used to produce it.|ACDD, CF |Yes|No|
|Root |  source | The method of production of the original data. If it was model-generated, source should name the model and its version. If it is observational, source should characterize it. |ACDD, CF|Yes|Yes|
|Root |  project_name | The name of the project(s) principally responsible for originating this data. Multiple projects can be separated by commas.|EBV|Yes|No|
|Root |  project_url | The URL(s) of the project(s).|EBV|Yes|No|
|Root |  date_created |The date on which this version of the data was created.| ACDD |Yes|Yes|
|Root |  date_issued |The date on which this data (including all modifications) was formally issued (i.e., made available to a wider audience) at the EBV Data Portal. |ACDD|No|-|
|Root |  creator_name|The name of the person principally responsible for creating this data.| ACDD|Yes|Yes|
|Root |   creator_email |The email address of the person principally responsible for creating this data.|ACDD |Yes|No|
|Root |   creator_institution |The institution of the creator; should uniquely identify the creator's institution. |ACDD |Yes|Yes|
|Root |   contributor_name |The name of any individuals, projects, or institutions that contributed to the creation of this data.|ACDD|Yes|No|
|Root |   publisher_name | The name of the person responsible for publishing the data file or product to users, with its current metadata and format. |ACDD|Yes|Yes|
|Root |   publisher_email |The email address of the person responsible for publishing the data file or product to users, with its current metadata and format. |ACDD|Yes|Yes|
|Root |   publisher_institution| The institution that presented the data file or equivalent product to users; should uniquely identify the institution.| ACDD|Yes|Yes|
|Root |   license |Provide the URL to a standard or specific license, enter "Freely Distributed" or "None", or describe any restrictions to data access and distribution in free text. |ACDD|Yes|Yes|
|Root |   history |Provides an audit trail for modifications to the original data. |ACDD, CF|No|-|
|Root |   comment | Miscellaneous information about the data, not captured elsewhere.|ACDD|Yes|No|
|Root| keywords |A comma-separated list of key words and/or phrases.|ACDD|Yes|No|
|Root|	ebv_class|EBV Class of the dataset. |EBV|Yes|Yes|
|Root|	ebv_name|EBV Name of the dataset. |EBV|Yes|Yes|
|Root|  ebv_vocabulary | URL to controlled vocabulary for ebv_class and ebv_name. Fiexed value: 'https://portal.geobon.org/api/v1/ebv' |EBV|No|-|
|Root|  ebv_domain |Environmental domain of the dataset, one or several of ‘Terrestrial’, ‘Marine’ or ‘Freshwater’.|EBV|Yes|Yes|
|Root|  ebv_cube_dimensions |Fixed value: 'lon, lat, time, entity' |EBV|No|-|

### 2.2 Scenario and Metric Attributes

|Level |Attribute|Comment|Convention|User Input|Mandatory|
| --- |  --- | --- | --- |--- | --- |
|Metric	| standard_name	|Short group name|CF|Yes|Yes|
|Metric|	long_name| Extensive group name / description|CF|Yes|Yes|
|Metric|	units| The units of the metrics's data. |CF|Yes|Yes|
|Scenario|	standard_name|Short group name|CF|(Yes|No|
|Scenario|	long_name|Extensive group name / description|CF|Yes|No|
|Root|  ebv_scenario_classification_name |Name of the applied scenario classification (if a scenario is used – not mandatory).|EBV|Yes|No|
|Root|  ebv_scenario_classification_version |Version of the scenario classification (if a scenario is used – not mandatory).|EBV|Yes|No|
|Root|  ebv_scenario_classification_url |URL of the scenario classification (if a scenario is used – not mandatory).|EBV|Yes|No|

### 2.3 ebv_cube variable attributes
|Level |Attribute|Comment|Convention|User Input|Mandatory|
| --- |  --- | --- | --- |--- | --- |
| ebv_cube | grid_mapping | Fixed value: '/crs' (Pointer to the coordinate reference system variable.) | CF|No|-|
| ebv_cube | long_name | Currently redundant to the standard_name of the corresponding metric. Will be updated in a future version. | CF|Yes|Yes|
| ebv_cube | coordinates | Fixed value: '/entity' (Pointer to the coordinate variable holding the string values.) | CF|No|-|
| ebv_cube | units | Currently redundant the units of the corresponding metric. Will be updated in a future version. | CF|Yes|Yes|
| ebv_cube | coverage_content_type | An ISO 19115-1 code to indicate the source of the data (image, thematicClassification, physicalMeasurement, auxiliaryInformation, qualityInformation, referenceInformation, modelResult, or coordinate). | CF|Yes|Yes|
| ebv_cube | _FillValue | internal netCDF attribute | netCDF Convention, CF |No|-|
| ebv_cube | _ChunkSizes | internal netCDF attribute | netCDF Convention |No|-|

### 2.4 entity Variable Attributes
The entity variable is an auxiliary coordinate variable and stores all entity names as a character array.

|Level |Attribute|Comment|Convention|User Input|Mandatory|
| --- |  --- | --- | --- |--- | --- |
|entity| ebv_entity_type |EBV entity type, e.g., ‘Communities’.|EBV|Yes|Yes|
|entity| ebv_entity_scope |Specifies the entity scope in more detail, e.g., ‘Birds, Forest Birds, Non Forest Birds’|EBV|Yes|Yes|
|entity| ebv_entity_classification_name|Name of the classification system used for the entity types (optional). |EBV|Yes|No|
|entity| ebv_entity_classification_url |URL of the classification system used for the entity types (optional).|EBV|Yes|No|
|entity| units |Fixed value: '1' for 'unity' (udunits)|CF|No|-|
|entity| long_name |Fixed value: 'entity' |CF|No|-|

### 2.5 Spatial Attributes
|Level |Attribute|Comment|Convention|User Input|Mandatory|
| --- |  --- | --- | --- |--- | --- |
| crs | grid_mapping_name | String value that contains the mapping’s name, e.g., WGS84 has the value 'latitude_longitude'.  | CF |No|-|
| crs | * | Attributes that define a specific mapping depend on the value of ‘grid_mapping_name’ (FGDC "Content Standard for Digital Geospatial Metadata"), e.g., for WGS84: ‘longitude_of_prime_meridian’, ‘semi_major_axis’ and ‘inverse_flattening’.| CF|No|-|
| crs | spatial_ref |  WKT2 representation of CRS |GDAL|No|-|
| crs | GeoTransform |  GeoTransform array: 'x_ul x_res x_rotation y_ul y_rotation y_res' |GDAL|No|-|
| crs | long_name | Fixed value: 'CRS definition'|CF| No|-|
| lon | axis |Fixed value: 'X' |CF|No|-|
| lon | units | 'degree_east' or 'meter' |CF|No|-|
| lon | standard_name | 'longitude' or 'projection_x_coordinate' |CF|No|-|
| lon | long_name | 'lon' |CF|No|-|
| lat | axis | Fixed value: 'Y' |CF|No|-|
| lat | units | 'degree_north' or 'meter' |CF|No|-|
| lat | standard_name | 'latitude' or 'projection_x_coordinate' |CF|No|-|
| lat | long_name | 'lat' |CF|No|-|
|Root|  ebv_spatial_scope |Spatial scope of the dataset, either ‘Continental/ Regional’, ‘National’, ‘Sub-national/Local’ or ‘Global’.|EBV|Yes|Yes|
|Root|  ebv_spatial_description |Specific information about the spatial scope.|EBV|Yes|Yes|
|Root| geospatial_bounds_crs|The coordinate reference system (CRS) of the point coordinates in the geospatial_bounds attribute. EPSG CRSs are strongly recommended. Example: 'EPSG:4326'|ACDD|No|-|
| Root | geospatial_bounds | Describes the data's 2D or 3D geospatial extent in OGC's Well-Known Text (WKT) Geometry format (reference the OGC Simple Feature Access (SFA) specification). Example: 'POLYGON ((40.26 -111.29, 41.26 -111.29, 41.26 -110.29, 40.26 -110.29, 40.26 -111.29))'|ACDD|No|-|
| Root | geospatial_lat_resolution  | Information about the targeted spacing of points in latitude. Describes the resolution as a numeric value and its units. |ACDD|No|-|
| Root | geospatial_lon_resolution | Information about the targeted spacing of points in longitude. Describes the resolution as a numeric value and its units. |ACDD|No|-|
| Root | geospatial_lat_units | Units for the longitude axis. These are presumed to be ‘degrees_north’ or ‘meters_north’. |ACDD|No|-|
| Root | geospatial_lon_units | Units for the longitude axis. These are presumed to be ‘degrees_east’ or ‘meters_east’. |ACDD|No|-|

### 2.6 Temporal Attributes
|Level |Attribute|Comment|Convention|User Input|Mandatory|
| --- |  --- | --- | --- |--- | --- |
| time | axis | Fixed value:  'T' | CF |No|-|
| time | calender | Fixed value: 'standard' (Gregorian) | CF|No|-|
| time | units | Fixed value:  'days since 1860-01-01 00:00:00.0' | CF |No|-|
| time | long_name | Fixed value:  'time' | CF |No|-|
| time | _ChunkSizes |internal netCDF attribute | netCDF Convention|No|-|
| Root | time_coverage_start | Describes the time of the first data point in the data set (ISO 8601:2004 date format). |ACDD|Yes|Yes|
| Root | time_coverage_end | Describes the time of the last data point in the data set (ISO 8601:2004 date format). |ACDD|Yes|Yes|
| Global | time_coverage_resolution | Describes the targeted time period between each value in the data set (ISO 8601:2004 date format). |ACDD|Yes|Yes|
