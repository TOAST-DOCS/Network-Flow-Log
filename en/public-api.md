## Network > Flow Log > API v2 Guide

To use the API, API endpoint and token are required. Refer to [API usage preparations](/Compute/Compute/ko/identity-api/) to prepare the information required to use the API.

The logger and logging port APIs use endpoints of type `network`. See the `serviceCatalog` in the token issuance response for the exact endpoint.

| Type | Region | Endpoint |
| --- | --- | ----- |
| network | Korea (Pangyo) Region<br>Korea (Pyeongchon) region | [https://kr1-api-network-infrastructure.nhncloudservice.com](https://kr1-api-network-infrastructure.nhncloudservice.com)<br>[https://kr2-api-network-infrastructure.nhncloudservice.com](https://kr2-api-network-infrastructure.nhncloudservice.com) |

Fields not specified in the guide may appear in the API response. Do not use these fields as they are for NHN Cloud internal use and are subject to change without notice.


## Flow log logger

### View a list of flow log loggers

```
GET /v2.0/flowlog-loggers
X-Auth-Token: {tokenId} 
```

#### Request

This API does not require a request body.

| Name | Type | Format | Required | Description |
| --- | --- | --- | --- | --- |
| tokenId | Header | String | O | Token ID |
| id | Query | UUID | - | Flow log logger ID to look up |
| name | Query | String | - | Flow log logger name to look up |
| resource_type | Query | String | - | Resource type of the flow log logger to look up |
| Resource ID | Query | String | - | Resource ID of the flow log logger to look up |
| filter | Query | String | - |  Filters for flow log loggers to look up |
| aggregation_interval | Query | Integer | - |  Aggregation interval of the flow log logger to look up |
| storage_type | Query | String | - |  Storage type of flow log logger to look up |
| log_format | Query | String | - |  Storage format of the flow log logger to look up |
| compression_type | Query | String | - |  Compression type of the flow log logger to look up |
| partitioned_period | Query | String | - | Partition period of the flow log logger to look up  |
| String | Query | String | - | Status of the flow log logger to look up |

#### Response

| Name | Type | Format | Description |
| --- | --- | --- | --- |
| flowlog_loggers | Body | Array | Flow log object list |
| flowlog_loggers.name | Body | String | Flow log logger name |
| flowlog_loggers.resource_type | Body | String | The type of resource the flow log logger collects from. One of `VPC`, `SUBNET`, or `PORT`. |
| flowlog_loggers.resource_id | Body | UUID | Flow log logger's collection target resource ID |
| flowlog_loggers.filter | Body | String | The collection target filter for the flow log logger. One of `ALL`, `ACCEPT`, or `DROP`. <br>* `ACCEPT` only captures packets that are allowed to communicate<br>* `DROP` only captures packets that are blocked from communication<br>* `ALL` captures all packets that are allowed to communicate and those that are blocked |
| flowlog_loggers.aggregation_interval | Body | Integer | How often to sum and aggregate the data collected by the flow log logger and write it to a file in storage. The unit is minutes. A file is created in storage with that value at the specified interval.  |
| flowlog_loggers.connection_setup_only | Body | Boolean | If `true`, collect only packets that attempt to establish a connection. When set to `true`, the collection is limited as follows.<br>\* For TCP, no longer collect TCP state of established<br>\* For UDP/ICMP, does not collect response packets |
| flowlog_loggers.storage_type | Body | Boolean | Storage type for the flow log logger. Currently only `OBS` is supported. |
| flowlog_loggers.storage_url | Body | Boolean | Storage address of the flow log logger |
| flowlog_loggers.log_format | Body | Boolean | Format of the file to be saved by the flow log logger. Can be `CSV`, `PARQUET` file format. |
| flowlog_loggers.compression_type | Body | Boolean | The compression format of the file to be saved by the flow log logger. Can be `RAW` or `GZIP` compression type. Currently, only `RAW` is supported.  |
| flowlog_loggers.customized_field | Body | String | Fields that the flow log logger will write to a file. Currently not supported. |
| flowlog_loggers.partition_period | Body | Boolean | When the flow log logger saves files to storage, it refers to the folder creation structure. Supports `HOUR` and `DAY`. <br>\* If you specify `DAY`, it creates a `{YEAR}/{MONTH}/{DAY}` folder under the directory-path of the storage_url you entered.<br>\* If you specify `HOUR`, it creates a folder under the directory-path of the storage_url you entered, up to `{YEAR}/{MONTH}/{DAY}/{HOUR}`, separated by time. |
| flowlog_loggers.admin_state_up | Body | Boolean | Enable status of the flow log logger. If `false`, it is disabled and does not collect. |
| flowlog_loggers.description | Body | Boolean | Description of the flow log logger |
| flowlog_loggers.status | Body | Enum | Status of the flow log logger |
| flowlog_loggers.created_at | Body | Date | Time the flow log logger was created |
| flowlog_loggers.updated_at | Body | Date | Time the flow log logger was modified |
| flowlog_loggers.error_type | Body | String | If the flow log logger encountered an error, display the reason for the error. <br>For more information, see the error types at the bottom of the page.|

<details>
  <summary>Example</summary>

```json
{
    "flowlog_loggers": [
        {
            "status": "ACTIVE",
            "connection_setup_only": false,
            "description": "",
            "partition_period": "DAY",
            "resource_id": "12799b52-0c81-4820-8fe6-b4963989ffe1",
            "tenant_id": "419a823563124dc5b5627f5e79db8174",
            "created_at": "2024-07-29 09:21:09",
            "error_type": "",
            "updated_at": "2024-08-04 05:11:05",
            "customized_field": "timestamp_start,timestamp_end,interface_id,vm_id,subnet_id,vpc_id,region,protocol,src_addr,dst_addr,src_port,dst_port,tcp_flag,packets,bytes,filter,status",
            "id": "3e84619a-1e49-4b19-b353-a15f7d278f94",
            "filter": "ACCEPT",
            "storage_type": "OBS",
            "aggregation_interval": 10,
            "admin_state_up": true,
            "log_format": "CSV",
            "storage_url": "https://kr2-api-object-storage.alpha-nhncloudservice.com/v1/AUTH_e670167936434f85a03694184000ffe6/flowlog-test/240729",
            "project_id": "419a823563124dc5b5627f5e79db8174",
            "compression_type": "RAW",
            "resource_type": "PORT",
            "name": "test"
        },
        {
            "status": "ACTIVE",
            "connection_setup_only": false,
            "description": "KR2test",
            "partition_period": "HOUR",
            "resource_id": "045e204c-4624-4b68-ac8a-7375989d3b79",
            "tenant_id": "419a823563124dc5b5627f5e79db8174",
            "created_at": "2024-08-04 05:17:03",
            "error_type": "",
            "updated_at": "2024-08-04 05:19:04",
            "customized_field": "timestamp_start,timestamp_end,interface_id,vm_id,subnet_id,vpc_id,region,protocol,src_addr,dst_addr,src_port,dst_port,tcp_flag,packets,bytes,filter,status",
            "id": "4a9912b9-7bbc-48f0-b066-fc165486f49d",
            "filter": "ALL",
            "storage_type": "OBS",
            "aggregation_interval": 1,
            "admin_state_up": true,
            "log_format": "CSV",
            "storage_url": "https://kr2-api-object-storage.alpha-nhncloudservice.com/v1/AUTH_e670167936434f85a03694184000ffe6/flowlog-test/240729",
            "project_id": "419a823563124dc5b5627f5e79db8174",
            "compression_type": "RAW",
            "resource_type": "PORT",
            "name": "flowlog-create-test"
        }
    ]
}
```

</details>

***

### Check the flow log loggers

```
GET /v2.0/flowlog-loggers/{flowlogLoggerId}
X-Auth-Token: {tokenId}
```

#### Request

This API does not require a request body.

| Name | Type | Format | Required | Description |
| --- | --- | --- | --- | --- |
| tokenId | Header | String | O | Token ID |
| flowlogLoggerId | URL | UUID | O | Flow log logger ID |

#### Response

| Name | In | Type | Description |
| ---- | --- | ---- | ----------- |
| flowlog_logger | Body | Object | Flow log logger information object |
| flowlog_logger.name | Body | String | Flow log logger name |
| flowlog_logger.resource_type | Body | String | The type of resource the flow log logger collects from. One of `VPC`, `SUBNET`, or `PORT`. |
| flowlog_logger.resource_id | Body | UUID | Flow log logger's collection target resource ID |
| flowlog_logger.filter | Body | String | The collection target filter for the flow log logger. One of `ALL`, `ACCEPT`, or `DROP`. <br>* `ACCEPT` only captures packets that are allowed to communicate<br>* `DROP` only captures packets that are blocked from communication<br>* `ALL` captures all packets that are allowed to communicate and those that are blocked |
| flowlog_logger.aggregation_interval | Body | Integer | How often to sum and aggregate the data collected by the flow log logger and write it to a file in storage. The unit is minutes. A file is created in storage with that value at the specified interval.  |
| flowlog_logger.connection_setup_only | Body | Boolean | If `true`, collect only packets that attempt to establish a connection. When set to `true`, the collection is limited as follows.<br>\* For TCP, no longer collect TCP state of established<br>\* For UDP/ICMP, does not collect response packets |
| flowlog_logger.storage_type | Body | Boolean | Storage type for the flow log logger. Currently only `OBS` is supported. |
| flowlog_logger.storage_url | Body | Boolean | Storage address of the flow log logger |
| flowlog_logger.log_format | Body | Boolean | Format of the file to be saved by the flow log logger. Can be `CSV`, `PARQUET` file format. |
| flowlog_logger.compression_type | Body | Boolean | The compression format of the file to be saved by the flow log logger. Can be `RAW` or `GZIP` compression type. Currently, only `RAW` is supported.  |
| flowlog_logger.customized_field | Body | String | Fields that the flow log logger will write to a file. Currently not supported. |
| flowlog_logger.partition_period | Body | Boolean | When the flow log logger saves files to storage, it refers to the folder creation structure. Supports `HOUR` and `DAY`. <br>\* If you specify `DAY`, it creates a `{YEAR}/{MONTH}/{DAY}` folder under the directory-path of the storage_url you entered.<br>\* If you specify `HOUR`, it creates a folder under the directory-path of the storage_url you entered, up to `{YEAR}/{MONTH}/{DAY}/{HOUR}`, separated by time. |
| flowlog_logger.admin_state_up | Body | Boolean | Enable status of the flow log logger. If `false`, it is disabled and does not collect. |
| flowlog_logger.description | Body | Boolean | Description of the flow log logger |
| flowlog_logger.status | Body | Enum | Status of the flow log logger |
| flowlog_logger.created_at | Body | Date | Time the flow log logger was created |
| flowlog_logger.updated_at | Body | Date | Time the flow log logger was modified |
| flowlog_logger.error_type | Body | String | If the flow log logger encountered an error, display the reason for the error. <br>For more information, see the error types at the bottom of the page. |

<details>
  <summary>Example</summary>

```json
{
    "flowlog_logger": {
        "status": "ACTIVE",
        "connection_setup_only": false,
        "description": "flowlog-create-description",
        "partition_period": "HOUR",
        "resource_id": "045e204c-4624-4b68-ac8a-7375989d3b79",
        "tenant_id": "419a823563124dc5b5627f5e79db8174",
        "created_at": "2024-08-04 05:22:57",
        "error_type": "",
        "updated_at": "2024-08-04 05:22:59",
        "customized_field": "timestamp_start,timestamp_end,interface_id,vm_id,subnet_id,vpc_id,region,protocol,src_addr,dst_addr,src_port,dst_port,tcp_flag,packets,bytes,filter,status",
        "id": "8287f6df-42cb-4cb6-a3f3-0e13fad43526",
        "filter": "ALL",
        "storage_type": "OBS",
        "aggregation_interval": 1,
        "admin_state_up": true,
        "log_format": "CSV",
        "storage_url": "https://kr2-api-object-storage.alpha-nhncloudservice.com/v1/AUTH_e670167936434f85a03694184000ffe6/flowlog-test/240729",
        "project_id": "419a823563124dc5b5627f5e79db8174",
        "compression_type": "RAW",
        "resource_type": "PORT",
        "name": "flowlog-create-test"
    }
}
```

</details>

***

### Create a flow log logger

```
POST /v2.0/flowlog-loggers
X-Auth-Token: {tokenId} 
```

#### Request

| Name | Type | Format | Required | Description |
| --- | --- | --- | --- | --- |
| tokenId | Header | String | O | Token ID |
| flowlog_logger | Body | Object | O | Flow log logger information object |
| flowlog_logger.name | Body | String | O | Flow log logger name |
| flowlog_logger.resource_type | Body | String |  | The type of resource the flow log logger collects. One of `VPC`, `SUBNET`, or `PORT`. If not entered, `PORT` is set. |
| flowlog_logger.resource_id | Body | UUID | O | Flow log logger's collect target resource ID |
| flowlog_logger.filter | Body | String |  | The collection target filter for the flow log logger. One of `ALL`, `ACCEPT`, or `DROP`. The default is `ALL`.<br>* `ACCEPT` only captures packets that are allowed to communicate<br>* `DROP` only captures packets that are blocked from communication<br>* `ALL` captures all packets that are allowed to communicate and those that are blocked |
| flowlog_logger.aggregation_interval | Body | Integer |  | How often to sum and aggregate the data collected by the flow log logger and write it to a file in storage. The unit is minutes. Files are created to storage at intervals of that value. Default is 10 minutes. |
| flowlog_logger.connection_setup_only | Body | Boolean |  | `If true,` collect only packets that attempted to establish a connection. When set to `true`, the collection is limited as follows. Default is `false`.<br>\* For TCP, no longer collect TCP state of established<br>\* For UDP/ICMP, does not collect response packets |
| flowlog_logger.storage_type | Body | Boolean | O | Storage type for the flow log logger. Currently `only OBS` is supported. |
| flowlog_logger.storage_url | Body | Boolean | O | The storage address of the flow log logger. If the storage type is `OBS`, you must enter all of `https://{object-storage-endpoint}/{AUTH-id}/{container}/{directory-path}`. |
| flowlog_logger.log_format | Body | Boolean |  | Format of the file to be saved by the flow log logger. Can be `CSV`, `PARQUET` file format. Default is `CSV`. |
| flowlog_logger.compression_type | Body | Boolean |  | The compression format of the file to be saved by the flow log logger. Can be `RAW` or `GZIP` compressed. Currently only `RAW` is supported. Default is `RAW`. |
| flowlog_logger.customized_field | Body | String |  | Fields for the flow log logger to write to a file |
| flowlog_logger.partition_period | Body | Boolean |  | When the flow log logger saves files to storage, it refers to the folder creation structure. Supports `HOUR and` `DAY`. Default is `DAY`. <br>\* If you specify `DAY`, it creates a `{YEAR}/{MONTH}/{DAY}` folder under the directory-path of the storage_url entered by the user.<br>\* If you specify `HOUR`, it creates a folder under the directory-path of the storage_url entered, up to {YEAR}/{MONTH}/{DAY}/{HOUR}`, separated by time. |
| flowlog_logger.admin_state_up | Body | Boolean |  | Enable status of the flow log logger. If `false`, it is disabled and does not collect. Default is `true`. |
| flowlog_logger.description | Body | Boolean |  | Description of the flow log logger |

<details>
  <summary>Example</summary>

```json
{
    "flowlog_logger": {
        "name": "flowlog-create-test",
        "resource_type": "PORT",
        "resource_id": "045e204c-4624-4b68-ac8a-7375989d3b79",
        "filter": "ALL",
        "aggregation_interval": 1,
        "connection_setup_only": false,
        "storage_type": "OBS",
        "storage_url": "https://kr2-api-object-storage.alpha-nhncloudservice.com/v1/AUTH_e670167936434f85a03694184000ffe6/flowlog-test/240729",
        "log_format": "CSV",
        "compression_type": "RAW",
        "customized_field": "timestamp_start,timestamp_end,interface_id,vm_id,subnet_id,vpc_id,region,protocol,src_addr,dst_addr,src_port,dst_port,tcp_flag,packets,bytes,filter,status",
        "partition_period": "HOUR",
        "admin_state_up": true,
        "description": "KR2 alpha cloud trail test"
    }
}
```

</details>

#### Response

| Name | Type | Format | Description |
| ---- | --- | ---- | --- |
| flowlog_logger | Body | Object | Flow log logger information object |
| flowlog_logger.name | Body | String | Flow log logger name |
| flowlog_logger.resource_type | Body | String | The type of resource the flow log logger collects from. One of `VPC`, `SUBNET`, or `PORT`. |
| flowlog_logger.resource_id | Body | UUID | Flow log logger's collect target resource ID |
| flowlog_logger.filter | Body | String | The collection target filter for the flow log logger. One of `ALL`, `ACCEPT`, or `DROP`. <br>* `ACCEPT` only captures packets that are allowed to communicate<br>* `DROP` only captures packets that are blocked from communication<br>* `ALL` captures all packets that are allowed to communicate and those that are blocked |
| flowlog_logger.aggregation_interval | Body | Integer | How often to sum and aggregate the data collected by the flow log logger and write it to a file in storage. The unit is minutes. A file is created in storage with that value at intervals.  |
| flowlog_logger.connection_setup_only | Body | Boolean | `If true,` collect only packets that attempt to establish a connection. When set to `true`, the collection is limited as follows.<br>\* For TCP, no longer collect TCP state of established<br>\* For UDP/ICMP, does not collect response packets |
| flowlog_logger.storage_type | Body | Boolean | Storage type for the flow log logger. Currently `only OBS` is supported. |
| flowlog_logger.storage_url | Body | Boolean | Storage address of the flow log logger |
| flowlog_logger.log_format | Body | Boolean | Format of the file to be saved by the flow log logger. Can be `CSV`, `PARQUET` file format. |
| flowlog_logger.compression_type | Body | Boolean | The compression format of the file to be saved by the flow log logger. Can be `RAW` or `GZIP` compressed. Currently `only RAW` is supported.  |
| flowlog_logger.customized_field | Body | String | Fields that the flow log logger will write to a file. Currently not supported. |
| flowlog_logger.partition_period | Body | Boolean | When the flow log logger saves files to storage, it refers to the folder creation structure. Supports `HOUR and` `DAY`. <br>\* If you specify `DAY`, it creates a `{YEAR}/{MONTH}/{DAY}` folder under the directory-path of the storage_url entered by the user.<br>\* If you specify `HOUR`, it creates a folder under the directory-path of the storage_url entered`, up to {YEAR}/{MONTH}/{DAY}/{HOUR}`, separated by time. |
| flowlog_logger.admin_state_up | Body | Boolean | Enable status of the flow log logger. If `false`, it is disabled and does not collect. |
| flowlog_logger.description | Body | Boolean | Description of the flow log logger |
| flowlog_logger.status | Body | Enum | Status of the flow log logger |
| flowlog_logger.created_at | Body | Date | Time the flow log logger was created |
| flowlog_logger.updated_at | Body | Date | Time the flow log logger was modified |
| flowlog_logger.error_type | Body | String | If the flow log logger encountered an error, display the reason for the error. <br>For more information, see at the bottom of the page.|

<details>
  <summary>Example</summary>

```json
{
    "flowlog_logger": {
        "status": "BUILD",
        "connection_setup_only": false,
        "description": "KR2 alpha cloud trail test",
        "partition_period": "HOUR",
        "resource_id": "045e204c-4624-4b68-ac8a-7375989d3b79",
        "tenant_id": "419a823563124dc5b5627f5e79db8174",
        "created_at": "2024-08-04 04:46:33",
        "error_type": "",
        "updated_at": "2024-08-04 04:46:33",
        "customized_field": "timestamp_start,timestamp_end,interface_id,vm_id,subnet_id,vpc_id,region,protocol,src_addr,dst_addr,src_port,dst_port,tcp_flag,packets,bytes,filter,status",
        "id": "08288f3c-d535-4343-9998-8d1d76a5c8a1",
        "filter": "ALL",
        "storage_type": "OBS",
        "aggregation_interval": 1,
        "admin_state_up": true,
        "log_format": "CSV",
        "storage_url": "https://kr2-api-object-storage.alpha-nhncloudservice.com/v1/AUTH_e670167936434f85a03694184000ffe6/flowlog-test/240729",
        "project_id": "419a823563124dc5b5627f5e79db8174",
        "compression_type": "RAW",
        "resource_type": "PORT",
        "name": "flowlog-create-test"
    }
}
```

</details>

***

### Edit a flow log logger

```
PUT /v2.0/flowlog-loggers/{flowlogLoggerId}
X-Auth-Token: {tokenId} 
```

#### Request

| Name | Type | Format | Required | Description |
| --- | --- | --- | --- | --- |
| tokenId | Header | String | O | Token ID |
| flowlogLoggerId | URL | UUID | O | Flow log logger ID |
| flowlog_logger | Body | Object | O | Flow log logger information object |
| flowlog_logger.name | Body | String | O | Flow log logger name |
| flowlog_logger.admin_state_up | Body | Boolean |  | Enable status of the flow log logger. If `false`, it is disabled and does not collect. Default is `true`. |
| flowlog_logger.description | Body | Boolean |  | Description of the flow log logger |

<details>
  <summary>Example</summary>

```json
{
    "flowlog_logger": {
        "name": "flowlog-name-updated",
        "description": "flowlog-description-updated",
        "admin_state_up": false
    }
}
```

</details>

#### Response

| Name | Type | Format | Description |
| ---- | --- | ---- | --- |
| flowlog_logger | Body | Object | Flow log logger information object |
| flowlog_logger.name | Body | String | Flow log logger name |
| flowlog_logger.resource_type | Body | String | The type of resource the flow log logger collects from. One of `VPC`, `SUBNET`, or `PORT`. |
| flowlog_logger.resource_id | Body | UUID | Flow log logger's collect target resource ID |
| flowlog_logger.filter | Body | String | The collection target filter for the flow log logger. One of `ALL`, `ACCEPT`, or `DROP`. <br>* `ACCEPT` only captures packets that are allowed to communicate<br>* `DROP` only captures packets that are blocked from communication<br>* `ALL` captures all packets that are allowed to communicate and those that are blocked |
| flowlog_logger.aggregation_interval | Body | Integer | How often to sum and aggregate the data collected by the flow log logger and write it to a file in storage. The unit is minutes. A file is created in storage with that value at intervals.  |
| flowlog_logger.connection_setup_only | Body | Boolean | `If true,` collect only packets that attempt to establish a connection. When set to `true`, the collection is limited as follows.<br>\* For TCP, no longer collect TCP state of established<br>\* For UDP/ICMP, does not collect response packets |
| flowlog_logger.storage_type | Body | Boolean | Storage type for the flow log logger. Currently `only OBS` is supported. |
| flowlog_logger.storage_url | Body | Boolean | Storage address of the flow log logger |
| flowlog_logger.log_format | Body | Boolean | Format of the file to be saved by the flow log logger. Can be `CSV`, `PARQUET` file format. |
| flowlog_logger.compression_type | Body | Boolean | The compression format of the file to be saved by the flow log logger. Can be `RAW` or `GZIP` compressed. Currently `only RAW` is supported.  |
| flowlog_logger.customized_field | Body | String | Fields that the flow log logger will write to a file. Currently not supported. |
| flowlog_logger.partition_period | Body | Boolean | When the flow log logger saves files to storage, it refers to the folder creation structure. Supports `HOUR and` `DAY`. <br>\* If you specify `DAY`, it creates a `{YEAR}/{MONTH}/{DAY}` folder under the directory-path of the storage_url entered by the user.<br>\* If you specify `HOUR`, it creates a folder under the directory-path of the storage_url entered`, up to {YEAR}/{MONTH}/{DAY}/{HOUR}`, separated by time. |
| flowlog_logger.admin_state_up | Body | Boolean | Enable status of the flow log logger. If `false`, it is disabled and does not collect. |
| flowlog_logger.description | Body | Boolean | Description of the flow log logger |
| flowlog_logger.status | Body | Enum | Status of the flow log logger |
| flowlog_logger.created_at | Body | Date | Time the flow log logger was created |
| flowlog_logger.updated_at | Body | Date | Time the flow log logger was modified |
| flowlog_logger.error_type | Body | String | If the flow log logger encountered an error, display the reason for the error. <br>For more information, see at the bottom of the page.|

<details>
  <summary>Example</summary>

```json
{
    "flowlog_logger": {
        "status": "INACTIVE",
        "connection_setup_only": false,
        "description": "flowlog-description-updated",
        "partition_period": "HOUR",
        "resource_id": "045e204c-4624-4b68-ac8a-7375989d3b79",
        "tenant_id": "419a823563124dc5b5627f5e79db8174",
        "created_at": "2024-08-04 05:22:57",
        "error_type": "",
        "updated_at": "2024-08-04 05:27:35.226363",
        "customized_field": "timestamp_start,timestamp_end,interface_id,vm_id,subnet_id,vpc_id,region,protocol,src_addr,dst_addr,src_port,dst_port,tcp_flag,packets,bytes,filter,status",
        "id": "8287f6df-42cb-4cb6-a3f3-0e13fad43526",
        "filter": "ALL",
        "storage_type": "OBS",
        "aggregation_interval": 1,
        "admin_state_up": false,
        "log_format": "CSV",
        "storage_url": "https://kr2-api-object-storage.alpha-nhncloudservice.com/v1/AUTH_e670167936434f85a03694184000ffe6/flowlog-test/240729",
        "project_id": "419a823563124dc5b5627f5e79db8174",
        "compression_type": "RAW",
        "resource_type": "PORT",
        "name": "flowlog-name-updated"
    }
}
```

</details>

***

### Delete a flow log logger

```
DELETE /v2.0/flowlog-loggers/{flowlogLoggerId}
X-Auth-Token: {tokenId} 
```

#### Request

This API does not require a request body.

| Name | Type | Format | Required | Description |
| --- | --- | --- | --- | --- |
| tokenId | Header | String | O | Token ID |
| flowlogLoggerId | URL | UUID | O | Flow log logger ID |

#### Response

Stops the specified node group.

<br>
<br>
<br>


## Flow log logging port

* Flow log logging ports are the ports that the flow log logger actually captures. If the resource_type of a flow logger is VPC or Subnet, a single flow log logger will manage multiple flow log logging ports.
* When a user creates or deletes a logger, Flow Log internally checks the ports that belong to that logger and adds or deletes them as logging port targets. This eliminates the need for the user to add or delete logging ports separately.
* The flow log logging port provides a lookup API only.

### View a list of flow log logging ports

```
GET /v2.0/flowlog-logging-ports
X-Auth-Token: {tokenId} 
```

#### Request

This API does not require a request body.

| Name | Type | Format | Required | Description |
| --- | --- | --- | --- | --- |
| tokenId | Header | String | O | Token ID |
| id | Query | UUID | - | ID of the flow log logging port to look up |
| logger_id | Query | UUID | - | The logger ID of the flow log logging port to look up |
| port_id | Query | UUID | - | The port ID of the flow log logging port to look up |
| network_id | Query | UUID | - | VPC ID of the flow log to look up |

#### Response

| Name | Type | Format | Description |
| --- | --- | --- | --- |
| flowlog_logging_ports | Array | Object | Flow log logging object list |
| flowlog_logging_ports.id | Body | UUID | Flow log logging port ID |
| flowlog_logging_ports.logger_id | Body | UUID | The ID of the flow log logger to which this flow log logging port belongs |
| flowlog_logging_ports.port_id | Body | UUID | The ID of the port currently logging |
| flowlog_logging_ports.network_id | Body | UUID | Network ID |
| flowlog_logging_ports.created_at | Body | Date | Time the flow log logging port was created |
| flowlog_logging_ports.updated_at | Body | Date | Time the flow log logging port was modified |

<details>
  <summary>Example</summary>

```json
{
    "flowlog_logging_ports": [
        {
            "network_id": "0afbf332-3432-450f-a25a-3cfa56b886d9",
            "tenant_id": "419a823563124dc5b5627f5e79db8174",
            "created_at": "2024-07-29 09:21:09",
            "updated_at": "2024-07-29 09:21:09",
            "logger_id": "3e84619a-1e49-4b19-b353-a15f7d278f94",
            "project_id": "419a823563124dc5b5627f5e79db8174",
            "port_id": "12799b52-0c81-4820-8fe6-b4963989ffe1",
            "id": "6e97b87a-21ac-42d2-afc7-6d180ba93417"
        },
        {
            "network_id": "0afbf332-3432-450f-a25a-3cfa56b886d9",
            "tenant_id": "419a823563124dc5b5627f5e79db8174",
            "created_at": "2024-08-04 05:22:57",
            "updated_at": "2024-08-04 05:22:57",
            "logger_id": "8287f6df-42cb-4cb6-a3f3-0e13fad43526",
            "project_id": "419a823563124dc5b5627f5e79db8174",
            "port_id": "045e204c-4624-4b68-ac8a-7375989d3b79",
            "id": "bb036e03-ce55-48d0-aea3-685bfbee24d0"
        }
    ]
}
```

</details>

***

### Check the flow log logging port

```
GET /v2.0/flowlog-logging-ports/{flowlogLoggingPortId}
X-Auth-Token: {tokenId} 
```

#### Request

This API does not require a request body.

| Name | Type | Format | Required | Description |
| --- | --- | --- | --- | --- |
| tokenId | Header | String | O | Token ID |
| flowlogLoggingPortId | URL | UUID | O | Flow log logging port ID |

#### Response

| Name | Type | Format | Description |
| --- | --- | --- | --- |
| flowlog_logging_port | Body | Object | Flow log logging object |
| flowlog_logging_port.id | Body | UUID | Flow log logging port ID |
| flowlog_logging_port.logger_id | Body | UUID | The ID of the flow log logger to which this flow log logging port belongs |
| flowlog_logging_port.port_id | Body | UUID | The ID of the port currently logging |
| flowlog_logging_port.network_id | Body | UUID | Network ID |
| flowlog_logging_port.created_at | Body | Date | Time the flow log logging port was created |
| flowlog_logging_port.updated_at | Body | Date | Time the flow log logging port was modified |

<details>
  <summary>Example</summary>

```json
{
    "flowlog_logging_port": {
        "network_id": "0afbf332-3432-450f-a25a-3cfa56b886d9",
        "tenant_id": "419a823563124dc5b5627f5e79db8174",
        "created_at": "2024-07-29 09:21:09",
        "updated_at": "2024-07-29 09:21:09",
        "logger_id": "3e84619a-1e49-4b19-b353-a15f7d278f94",
        "project_id": "419a823563124dc5b5627f5e79db8174",
        "port_id": "12799b52-0c81-4820-8fe6-b4963989ffe1",
        "id": "6e97b87a-21ac-42d2-afc7-6d180ba93417"
    }
}
```

</details>

<br><br><br>

## Error type

If the environment you are trying to use the flow log in is not set up correctly, you might get an error. In this case, you can look up flowlog_logger.error_type to determine the cause of the error.


The status and error types of the flow log logger are as follows.

| Flow log logger status | Error type | Error cause | Checklist |
| :---: | --- | --- | --- |
| ACTIVE | - | - | - | - |
| BUILD | - | - | - | - |
| ERROR | AuthenticationSystemError | There's a problem with the authentication system. Please contact the Help Center. | The flow log system account did not receive token from the Keystone server. |
| ERROR | OBSConfigurationError | Check the OBS URL and access policy. | Dummy data was sent to the user's storage but a 403 error was returned because there was no access to OBS. Check the container URL and access policy. |
| ERROR | OBSServiceNotAvailableError | The OBS service is not working, please contact the Customer Center. | Dummy data was sent to the user's storage but an error was returned other than 401 and 403. |


