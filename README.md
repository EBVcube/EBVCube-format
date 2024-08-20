# EBVCube-format
Definition of the EBVCube netCDF format. 

## Basis:
* netCDF Convention: https://www.unidata.ucar.edu/software/netcdf/documentation/4.7.4-pre/attribute_conventions.html
* CF convention 1.8: https://cfconventions.org/Data/cf-conventions/cf-conventions-1.8/cf-conventions.html
* Additional attribtues from the ACDD 1.3 terms: https://wiki.esipfed.org/Attribute_Convention_for_Data_Discovery_1-3
* EBV framework https://geobon.org/ebvs/what-are-ebvs/

## 1 Internal hierarchy
* Two possible nested sub-groups: scenario and metric
* Metric is a mandatory group, scenario is optional
* The scenario group is always higher than the metric
* If several metrics are present, they need to be repeated for all scenarios

### 1.1 Example 1 (extensive)
* Note: there would be more scenarios and/or metrics possible
``` bash
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

### 1.2 Example 2 (minimal)
``` bash
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
The following tables describe the attributes of the EBV netCDF files. Each table corresponds to a different component in the netCDF. The descriptions of the attributes that are derived from the ACDD, are directly cited from the ACDD 1.3 documentation (https://wiki.esipfed.org/Attribute_Convention_for_Data_Discovery_1-3). 

### 2.1 Global Attributes
|Level | new Attribute|Comment|Convention|
| --- |  --- | --- | --- |
|Global |id | An identifier for the data set, provided by and unique within its naming authority. (Currently simple integer, future: maybe DOI) |ACDD|
|Global |  naming_authority| The organization that provides the initial id for the dataset. |ACDD|
|Global | Conventions| A comma-separated list of the conventions that are followed by the dataset. (Currently 'ACDD-1.3, CF-1.8, EBVNetCDF-1.0') |ACDD, CF|
|Global|		title|A short phrase or sentence describing the dataset.|ACDD, CF |
|Global|	summary|A paragraph describing the dataset, analogous to an abstract for a paper.|ACDD|
|Global |  references|Published or web-based references that describe the data or methods used to produce it.|ACDD, CF |
|Global |  source | The method of production of the original data. If it was model-generated, source should name the model and its version. If it is observational, source should characterize it. |ACDD, CF|
|Global |  project_name | The name of the project(s) principally responsible for originating this data. Multiple projects can be separated by commas.|EBV|
|Global |  project_url | The URL(s) of the project(s).|EBV|
|Global |  date_created |The date on which this version of the data was created.| ACDD |
|Global |  date_issued |The date on which this data (including all modifications) was formally issued (i.e., made available to a wider audience). |ACDD|
|Global |    creator_name|The name of the person principally responsible for creating this data.| ACDD|
|Global |   creator_email |The email address of the person principally responsible for creating this data.|ACDD |
|Global |   creator_institution |The institution of the creator; should uniquely identify the creator's institution. |ACDD |
|Global |   contributor_name |The name of any individuals, projects, or institutions that contributed to the creation of this data.|ACDD|
|Global |   publisher_name | The name of the person responsible for publishing the data file or product to users, with its current metadata and format. |ACDD|
|Global |   publisher_email |The email address of the person responsible for publishing the data file or product to users, with its current metadata and format. |ACDD|
|Global |   publisher_institution| The institution that presented the data file or equivalent product to users; should uniquely identify the institution.| ACDD|
|Global |   license |Provide the URL to a standard or specific license, enter "Freely Distributed" or "None", or describe any restrictions to data access and distribution in free text. |ACDD|
|Global |   history |Provides an audit trail for modifications to the original data. |ACDD, CF|
|Global |   comment | Miscellaneous information about the data, not captured elsewhere.|ACDD|
|Global| keywords |A comma-separated list of key words and/or phrases.|ACDD|
|Global|	ebv_class|EBV Class of the dataset. |EBV|
|Global|	ebv_name|EBV Name of the dataset. |EBV|
|Global|  ebv_vocabulary | https://portal.geobon.org/api/v1/ebv |EBV|
|Global|  ebv_scenario_classification_name |Name of the applied scenario classification (if a scenario is used – not mandatory).|EBV|
|Global|  ebv_scenario_classification_version |Version of the scenario classification (if a scenario is used – not mandatory).|EBV|
|Global|  ebv_scenario_classification_url |URL of the scenario classification (if a scenario is used – not mandatory).|EBV|
|Global|  ebv_spatial_scope |Spatial scope of the dataset, either ‘Continental/ Regional’, ‘National’, ‘Sub-national/Local’ or ‘Global’.|EBV|
|Global|  ebv_spatial_description |Specific information about the spatial scope.|EBV|
|Global|  ebv_domain |Environmental domain of the dataset, one or several of ‘Terrestrial’, ‘Marine’ or ‘Freshwater’.|EBV|
|Global|  ebv_cube_dimensions |Fixed value: "lon, lat, time, entity". |EBV|
|Global| geospatial_bounds_crs|The coordinate reference system (CRS) of the point coordinates in the geospatial_bounds attribute. EPSG CRSs are strongly recommended. Example: 'EPSG:4326'|ACDD|
| Global | geospatial_bounds | Describes the data's 2D or 3D geospatial extent in OGC's Well-Known Text (WKT) Geometry format (reference the OGC Simple Feature Access (SFA) specification). Example: 'POLYGON ((40.26 -111.29, 41.26 -111.29, 41.26 -110.29, 40.26 -110.29, 40.26 -111.29))'|ACDD|
| Global | geospatial_lat_resolution  | Information about the targeted spacing of points in latitude. Describes the resolution as a number value. |ACDD|
| Global | geospatial_lon_resolution | Information about the targeted spacing of points in longitude. Describes the resolution as a number value. |ACDD|
| Global | geospatial_lat_units | Units for the longitude axis. These are presumed to be ‘degrees_north’ or ‘meters_north’. |ACDD|
| Global | geospatial_lon_units | Units for the longitude axis. These are presumed to be ‘degrees_east’ or ‘meters_east’. |ACDD|
| Global | time_coverage_start | Describes the time of the first data point in the data set (ISO 8601:2004 date format). |ACDD|
| Global | time_coverage_end | Describes the time of the last data point in the data set (ISO 8601:2004 date format). |ACDD|
| Global | time_coverage_resolution | Describes the targeted time period between each value in the data set (ISO 8601:2004 date format). |ACDD|

### 2.2 Scenario and Metric Attributes
* Probable development in future: with the new 4D cube the metric group can be dropped or turned into a variable (i.e. replacing the ebv_cube). Will be considered in a future EBV netCDF standard version.

|Level | New Attribute | Comment |Convention|
| --- | --- | --- | --- |
|Metric	| standard_name	|Short group name|CF|
|Metric|	long_name| Extensive group name / description|CF|
|Metric|	units| Description of the units|CF|
|Scenario|	standard_name|Short group name|CF|
|Scenario|	long_name|Extensive group name / description|CF|

### 2.3 ebv_cube variable attributes
* note on _FillValue and _ChunkSizes: the underscore in the beginning indicates that no changes shall be done after the initial creation because the datacubes are prefilled with the fillvalue and chunked accordingly to the parameters given at creation time. The attributes guarantee transparancy for the user. An afterward change would not really change the data but only blur the existing set up. 

|Level | new Attribute | Comment | Convention|
| --- | --- | --- | --- |
| ebv_cube | grid_mapping | Fixed value: '/crs' (Pointer to the coordinate reference system variable.) | CF|
| ebv_cube | long_name | Currently redundant to the standard_name of the corresponding metric. Will be updated in a future version. | CF|
| ebv_cube | coordinate | Fixed value: '/entity' (Pointer to the coordinate variable holding the string values.) | CF|
| ebv_cube | units | Currently redundant the units of the corresponding metric. Will be updated in a future version. | CF|
| ebv_cube | coverage_content_type | An ISO 19115-1 code to indicate the source of the data (image, thematicClassification, physicalMeasurement, auxiliaryInformation, qualityInformation, referenceInformation, modelResult, or coordinate). | CF|
| ebv_cube | _FillValue | internal netCDF attribute | netCDF Convention, CF |
| ebv_cube | _ChunkSizes | internal netCDF attribute | netCDF Convention|

### 2.4 entity Variable Attributes
* stores character data: list off all entity names 
* Most files from the EBV Data Portal still have the value 'biological entity' for the attribute 'long_name'. Will be updated.

|Level | new Attribute | Comment | Convention|
| --- | --- | --- | --- |
|entity|  ebv_entity_type |EBV entity type, e.g., ‘Communities’.|EBV|
|entity|  ebv_entity_scope |Specifies the entity scope in more detail, e.g., ‘Birds, Forest Birds, Non Forest Birds’|EBV|
|entity| ebv_entity_classification_name|Name of the classification system used for the entity types (optional). |EBV|
|entity| ebv_entity_classification_url |URL of the classification system used for the entity types (optional).|EBV|
|entity| units |Fixed value: '1' (udunits) for 'unity' |CF|
|entity| long_name |Fixed value: 'entity' |CF|

### 2.5 Spatial Attributes
* Note: The variables (ebv_cube) need one attribute called 'grid_mapping'='crs' that points to this variable. For detailed information regading the grid mapping names, see: https://cfconventions.org/Data/cf-conventions/cf-conventions-1.8/cf-conventions.html#appendix-grid-mappings.

|Level | new Attribute | Comment | Convention|
| --- | --- | --- | --- |
| crs | grid_mapping_name | String value that contains the mapping’s name, e.g., WGS84 has the value 'latitude_longitude'.  | CF |
| crs | * | Attributes that define a specific mapping depend on the value of ‘grid_mapping_name’, e.g., for WGS84: ‘longitude_of_prime_meridian’, ‘semi_major_axis’ and ‘inverse_flattening’.| CF|
| crs | spatial_ref |  WKT2 representation of CRS |GDAL| 
| crs | GeoTransform |  GeoTransform array: 'x_ul x_res x_rotation y_ul y_rotation y_res' |GDAL| 
| crs | long_name |  'CRS definition'|CF|  
| lon | axis |Fixed value: 'X' |CF|
| lon | units | 'degrees_east' or 'meters' |CF|
| lon | standard_name | 'longitude' or 'projection_x_coordinate' |CF|
| lat | axis | Fixed value: 'Y' |CF|
| lat | units | 'degrees_north' or 'meters' |CF|
| lat | standard_name | 'latitude' or 'projection_x_coordinate' |CF|

### 2.6 Temporal Attributes
* Probable development in future: The 'calender' attribute value can and should be defined differently depending on the data. Will be considered in a future EBV netCDF standard version. See: https://cfconventions.org/Data/cf-conventions/cf-conventions-1.8/cf-conventions.html#calendar.

|Level | new Attribute| Comment | Convention|
| --- | --- | --- | --- |
| time | axis | Fixed value:  'T' | CF |
| time | calender | Fixed value: 'standard' (Gregorian) | CF|
| time | units | Fixed value:  'days since 1860-01-01 00:00:00.0' | CF |
| time | long_name | Fixed value:  'time' | CF |
| time | _ChunkSizes |internal netCDF attribute | netCDF Convention|
