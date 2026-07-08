<a id="network-flow-log-api-v2-guide"></a>
## Network > Flow Log > API v2 Guide { #network-flow-log-api-v2-guide }

The NHN Cloud Network service uses the IaaS token for authentication/authorization when making API calls. The IaaS token is the authentication token used by the NHN Cloud's OpenStack-based infrastructure service (IaaS). For more information on IaaS token issuance and usage, see [IaaS token](/nhncloud/ko/public-api/iaas-token).

The logger and logging port API uses the `network` type endpoint. To see the exact endpoint, refer to `serviceCatalog` of the token issuance response.

| Type | Region | Endpoint |
| --- | --- | ----- |
| network | Korea (Pangyo) region<br>Korea (Pyeongchon) region<br>Korea (Gwangju) region | [https://kr1-api-network-infrastructure.nhncloudservice.com](https://kr1-api-network-infrastructure.nhncloudservice.com)<br>[https://kr2-api-network-infrastructure.nhncloudservice.com](https://kr2-api-network-infrastructure.nhncloudservice.com)<br>[https://kr3-api-network-infrastructure.nhncloudservice.com](https://kr3-api-network-infrastructure.nhncloudservice.com) |

API response may show the fields not specified by the guide. These fields are internally used by NHN Cloud, and not used because they are subject to change without prior notice.


<a id="flow-log-logger"></a>
## Flow Log Logger { #flow-log-logger }

<a id="list-flow-log-loggers"></a>
### List Flow Log Loggers { #list-flow-log-loggers }

```
GET /v2.0/flowlog-loggers
X-Auth-Token: {tokenId}
```

<a id="list-flow-log-loggers-request"></a>
#### Request

This API does not require a request body.

| Name | Type | Format | Required | Description |
| --- | --- | --- | --- | --- |
| tokenId | Header | String | O | Token ID |
| id | Query | UUID | - | Flow Log Logger ID to query |
| name | Query | String | - | Flow Log logger name to query |
| resource_type | Query | String | - | Resource type of the Flow Log logger to query |
| resource_id | Query | String | - | Resource ID of the Flow Log logger to query |
| filter | Query | String | - |  Filter of the Flow Log logger to query |
| aggregation_interval | Query | Integer | - |  Aggregation interval of the Flow Log logger to query |
| storage_type | Query | String | - |  Storage type of the Flow Log logger to query |
| log_format | Query | String | - |  Log format of the Flow Log logger to query |
| compression_type | Query | String | - |  Compression type of the Flow Log logger to query |
| partitioned_period | Query | String | - | Partition period of the Flow Log logger to query  |
| customized_file_name | Query | String | - | File name format of the Flow Log logger to query |
| status | Query | String | - | Status of the Flow Log logger to query |

<a id="list-flow-log-loggers-response"></a>
#### Response

| Name | Type | Format | Description |
| --- | --- | --- | --- |
| flowlog_loggers | Body | Array | Flow Log object list |
| flowlog_loggers.name | Body | String | Flow Log logger name |
| flowlog_loggers.resource_type | Body | String | The target resource type of the Flow Log logger. One of the `VPC`, `SUBNET`, or `PORT`. |
| flowlog_loggers.resource_id | Body | UUID | Flow Log logger's target resource ID |
| flowlog_loggers.filter | Body | String | Filter to collect in Flow Log logger. One of the `ALL`, `ACCEPT`, or `DROP`. <br>* `ACCEPT` captures only packets that are allowed to communicate<br>* `DROP` captures only packets that are blocked to communicate<br>* `ALL` captures both allowed and blocked packets |
| flowlog_loggers.aggregation_interval | Body | Integer | The interval at which data collected by the Flow Log logger is aggregated and written as a file to the storage. The unit is minutes. Files are created in the storage at this interval.  |
| flowlog_loggers.connection_setup_only | Body | Boolean | If the value is `true`, only the packets attempting to establish the connection are collected. Setting this to `true` limits the collection targets as follows:<br>\* For TCP, packets are no longer collected when the TCP state is established<br>\* For UDP/ICMP, response packets are not collected |
| flowlog_loggers.storage_type | Body | String | The storage type of the Flow Log logger. Currently, only `OBS` is supported. |
| flowlog_loggers.storage_url | Body | String | Storage address of the Flow Log logger |
| flowlog_loggers.log_format | Body | String | The file format that the Flow Log logger will store. The file format can be `CSV` or `PARQUET`. |
| flowlog_loggers.compression_type | Body | String | Compression format of files stored by the Flow Log logger. Compressed formats `RAW` and `GZIP` are available. |
| flowlog_loggers.customized_field | Body | String | Field that the Flow Log logger will write to a file. <br>\* For the fields supported by Flow Log, see the Fields section under Statistical Information in the Flow Log Overview of the user guide.|
| flowlog_loggers.partition_period | Body | String | Refers to the folder creation structure when the Flow Log logger stores files in the storage. Supports `HOUR` and `DAY`. <br>\* If you specify `DAY`, a folder `#{year}/#{month}/#{day}` is created under the directory-path of storage_url entered by the user to separate the day<br>\* If you specify `HOUR`, a folder up to `#{year}/#{month}/#{day}/#{hour}` is created under the directory-path of storage_url entered by the user and separated by time <br> \* For other custom formats, the time is entered in #{year}, #{month}, #{day}, and #{hour}.|
| flowlog_loggers.customized_file_name | Body | String | The format of the file title when the Flow Log logger stores a file in a storage. <br> \* The default value is #{logger_id}_#{year}-#{month}-#{day}T#{hour}:#{minute}:#{second}KST |
| flowlog_loggers.admin_state_up | Body | Boolean | The enabled status of the Flow Log logger. If `false`, it is disabled and not collected. |
| flowlog_loggers.description | Body | String | Description of the Flow Log logger |
| flowlog_loggers.status | Body | Enum | Status of  the Flow Log logger |
| flowlog_loggers.created_at | Body | Date | Time the Flow Log logger was created |
| flowlog_loggers.updated_at | Body | Date | Time the Flow Log logger was modified |
| flowlog_loggers.error_type | Body | String | If an error occurs in the Flow Log logger, the reason for the error is displayed. <br>For more information, see the error types at the bottom of the page.|

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

<a id="view-flow-log-logger"></a>
### View Flow Log Logger { #view-flow-log-logger }

```
GET /v2.0/flowlog-loggers/{flowlogLoggerId}
X-Auth-Token: {tokenId}
```

<a id="view-flow-log-logger-request"></a>
#### Request

This API does not require a request body.

| Name | Type | Format | Required | Description |
| --- | --- | --- | --- | --- |
| tokenId | Header | String | O | Token ID |
| flowlogLoggerId | URL | UUID | O | Flow Log logger ID |

<a id="view-flow-log-logger-response"></a>
#### Response

| Name | Type | Format | Description |
| ---- | --- | ---- | ----------- |
| flowlog_logger | Body | Object | Flow Log logger information object |
| flowlog_logger.name | Body | String | Flow Log logger name |
| flowlog_logger.resource_type | Body | String | The target resource type of the Flow Log logger. One of the `VPC`, `SUBNET`, or `PORT`. |
| flowlog_logger.resource_id | Body | UUID | Flow Log logger's target resource ID |
| flowlog_logger.filter | Body | String | Filter to collect in Flow Log logger. One of the `ALL`, `ACCEPT`, or `DROP`. <br>* `ACCEPT` captures only packets that are allowed to communicate<br>* `DROP` captures only packets that are blocked to communicate<br>* `ALL` captures both allowed and blocked packets |
| flowlog_logger.aggregation_interval | Body | Integer | The interval at which data collected by the Flow Log logger is aggregated and written as a file to the storage. The unit is minutes. Files are created in the storage at this interval.  |
| flowlog_logger.connection_setup_only | Body | Boolean | If the value is `true`, only the packets attempting to establish the connection are collected. Setting this to `true` limits the collection targets as follows:<br>\* For TCP, packets are no longer collected when the TCP state is established<br>\* For UDP/ICMP, response packets are not collected |
| flowlog_logger.storage_type | Body | String | The storage type of the Flow Log logger. Currently, only `OBS` is supported. |
| flowlog_logger.storage_url | Body | String | Storage address of the Flow Log logger |
| flowlog_logger.log_format | Body | String | The file format that the Flow Log logger will store. The file format can be `CSV` or `PARQUET`. |
| flowlog_logger.compression_type | Body | String | Compression format of files stored by the Flow Log logger. Compressed formats `RAW` and `GZIP` are available. |
| flowlog_logger.customized_field | Body | String | Field that the Flow Log logger will write to a file. <br>\* For the fields supported by Flow Log, see the Fields section under Statistical Information in the Flow Log Overview of the user guide.|
| flowlog_logger.partition_period | Body | String | Refers to the folder creation structure when the Flow Log logger stores files in the storage. Supports `HOUR` and `DAY`. <br>\* If you specify `DAY`, a folder `#{year}/#{month}/#{day}` is created under the directory-path of storage_url entered by the user to separate the day<br>\* If you specify `HOUR`, a folder up to `#{year}/#{month}/#{day}/#{hour}` is created under the directory-path of storage_url entered by the user and separated by time <br> \* For other custom formats, the time is entered in #{year}, #{month}, #{day}, and #{hour}.|
| flowlog_logger.customized_file_name | Body | String | The format of the file title when the Flow Log logger stores a file in a storage. <br> \* The default value is #{logger_id}_#{year}-#{month}-#{day}T#{hour}:#{minute}:#{second}KST |
| flowlog_logger.admin_state_up | Body | Boolean | The enabled status of the Flow Log logger. If `false`, it is disabled and not collected. |
| flowlog_logger.description | Body | String | Description of the Flow Log logger |
| flowlog_logger.status | Body | Enum | Status of  the Flow Log logger |
| flowlog_logger.created_at | Body | Date | Time the Flow Log logger was created |
| flowlog_logger.updated_at | Body | Date | Time the Flow Log logger was modified |
| flowlog_logger.error_type | Body | String | If an error occurs in the Flow Log logger, the reason for the error is displayed. <br>For more information, see the error types at the bottom of the page. |

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

<a id="create-flow-log-logger"></a>
### Create Flow Log Logger { #create-flow-log-logger }

```
POST /v2.0/flowlog-loggers
X-Auth-Token: {tokenId}
```

<a id="create-flow-log-logger-request"></a>
#### Request

| Name | Type | Format | Required | Description |
| --- | --- | --- | --- | --- |
| tokenId | Header | String | O | Token ID |
| flowlog_logger | Body | Object | O | Flow Log logger information object |
| flowlog_logger.name | Body | String | O | Flow Log logger name |
| flowlog_logger.resource_type | Body | String |  | The target resource type of the Flow Log logger. One of the `VPC`, `SUBNET`, or `PORT`. If not entered, it is considered to be `PORT`. |
| flowlog_logger.resource_id | Body | UUID | O | Flow Log logger's target resource ID |
| flowlog_logger.filter | Body | String |  | Filter to collect in Flow Log logger. One of the `ALL`, `ACCEPT`, or `DROP`. The default value is `ALL`.<br>* `ACCEPT` captures only packets that are allowed to communicate<br>* `DROP` captures only packets that are blocked to communicate<br>* `ALL` captures both allowed and blocked packets |
| flowlog_logger.aggregation_interval | Body | Integer |  | The interval at which data collected by the Flow Log logger is aggregated and written as a file to the storage. The unit is minutes. Files are created in the storage at this interval. The default is 10 minutes. |
| flowlog_logger.connection_setup_only | Body | Boolean |  | If the value is `true`, only the packets attempting to establish the connection are collected. If set to `true`, the collection target is limited as follows. The default value is `false`<br>\* For TCP, packets are no longer collected when the TCP state is established<br>\* For UDP/ICMP, response packets are not collected |
| flowlog_logger.storage_type | Body | String | O | The storage type of the Flow Log logger. Currently, only `OBS` is supported. |
| flowlog_logger.storage_url | Body | String | O | The storage address of the Flow Log logger. If the storage type is `OBS`, you must enter all `https://{object-storage-endpoint}/{AUTH-id}/{container}/{directory-path}`. |
| flowlog_logger.log_format | Body | String |  | The file format that the Flow Log logger will store. The file format can be `CSV` and `PARQUET`. The default value is `CSV`. |
| flowlog_logger.compression_type | Body | String |  | Compression format of files stored by the Flow Log logger. Compressed formats `RAW` and `GZIP` are available. The default is `RAW`. |
| flowlog_logger.customized_field | Body | String |  | Fields that the Flow Log logger records in a file. <br>\* It must be written in the form of commas, as shown in the example below. <br>\* For the fields supported by Flow Log, see the Fields section under Statistical Information in the Flow Log Overview of the user guide. |
| flowlog_logger.partition_period | Body | String |  | Refers to the folder creation structure when the Flow Log logger stores files in the storage. Supports `HOUR` and `DAY`. <br>\* If you specify `DAY`, a folder `#{year}/#{month}/#{day}` is created under the directory-path of storage_url entered by the user to separate the day<br>\* If you specify `HOUR`, a folder up to `#{year}/#{month}/#{day}/#{hour}` is created under the directory-path of storage_url entered by the user and separated by time <br> \* For other custom formats, the time is entered in #{year}, #{month}, #{day}, and #{hour}. <br> \* Custom format only allows input of numbers, English characters, and some special characters (/, -, \_, =). Enter `/` to separate folders. <br> \* (e.g., enter `year=#{year}/month=#{month}/day=#{day}`) and store Flow Log files for September 1, 2024 under the folder `year=2024/month=09/day=01`|
| flowlog_logger.customized_file_name | Body | String | | The format of the file title when the Flow Log logger stores a file in a storage. <br> \* The default value is #{logger_id}_#{year}-#{month}-#{day}T#{hour}:#{minute}:#{second}KST <br> \* You can define the title of Flow Log logger file by using the template variables #{logger_id}, #{year}, #{month}, #{day}, #{hour}, #{minute}, and #{second}, respectively, precisely once and for each. |
| flowlog_logger.admin_state_up | Body | Boolean |  | Enabled status of the Flow Log logger. If `false`, it is disabled and not collected. The default value is `true`. |
| flowlog_logger.description | Body | String |  | Description of the Flow Log logger |

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

<a id="create-flow-log-logger-response"></a>
#### Response

| Name | Type | Format | Description |
| ---- | --- | ---- | --- |
| flowlog_logger | Body | Object | Flow Log logger information object |
| flowlog_logger.name | Body | String | Flow Log logger name |
| flowlog_logger.resource_type | Body | String | The target resource type of the Flow Log logger. One of the `VPC`, `SUBNET`, or `PORT`. |
| flowlog_logger.resource_id | Body | UUID | Flow Log logger's target resource ID |
| flowlog_logger.filter | Body | String | Filter to collect in Flow Log logger. One of the `ALL`, `ACCEPT`, or `DROP`. <br>* `ACCEPT` captures only packets that are allowed to communicate<br>* `DROP` captures only packets that are blocked to communicate<br>* `ALL` captures both allowed and blocked packets |
| flowlog_logger.aggregation_interval | Body | Integer | The interval at which data collected by the Flow Log logger is aggregated and written as a file to the storage. The unit is minutes. Files are created in the storage at this interval.  |
| flowlog_logger.connection_setup_only | Body | Boolean | If the value is `true`, only the packets attempting to establish the connection are collected. Setting this to `true` limits the collection targets as follows:<br>\* For TCP, packets are no longer collected when the TCP state is established<br>\* For UDP/ICMP, response packets are not collected |
| flowlog_logger.storage_type | Body | String | The storage type of the Flow Log logger. Currently, only `OBS` is supported. |
| flowlog_logger.storage_url | Body | String | Storage address of the Flow Log logger |
| flowlog_logger.log_format | Body | String | The file format that the Flow Log logger will store. The file format can be `CSV` or `PARQUET`. |
| flowlog_logger.compression_type | Body | String | Compression format of files stored by the Flow Log logger. Compressed formats `RAW` and `GZIP` are available. |
| flowlog_logger.customized_field | Body | String | Field that the Flow Log logger will write to a file.<br>\* For the fields supported by Flow Log, see the Fields section under Statistical Information in the Flow Log Overview of the user guide. |
| flowlog_logger.partition_period | Body | String | Refers to the folder creation structure when the Flow Log logger stores files in the storage. Supports `HOUR` and `DAY`. <br>\* If you specify `DAY`, a folder `#{year}/#{month}/#{day}` is created under the directory-path of storage_url entered by the user to separate the day<br>\* If you specify `HOUR`, a folder up to `#{year}/#{month}/#{day}/#{hour}` is created under the directory-path of storage_url entered by the user and separated by time <br> \* For other custom formats, the time is entered in #{year}, #{month}, #{day}, and #{hour}.|
| flowlog_logger.customized_file_name | Body | String | The format of the file title when the Flow Log logger stores a file in a storage. <br> \* The default value is #{logger_id}_#{year}-#{month}-#{day}T#{hour}:#{minute}:#{second}KST |
| flowlog_logger.admin_state_up | Body | Boolean | The enabled status of the Flow Log logger. If `false`, it is disabled and not collected. |
| flowlog_logger.description | Body | String | Description of the Flow Log logger |
| flowlog_logger.status | Body | Enum | Status of  the Flow Log logger |
| flowlog_logger.created_at | Body | Date | Time the Flow Log logger was created |
| flowlog_logger.updated_at | Body | Date | Time the Flow Log logger was modified |
| flowlog_logger.error_type | Body | String | If an error occurs in the Flow Log logger, the reason for the error is displayed. <br>For more information, see the error types at the bottom of the page.|

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

<a id="modify-flow-log-logger"></a>
### Modify Flow Log Logger { #modify-flow-log-logger }

```
PUT /v2.0/flowlog-loggers/{flowlogLoggerId}
X-Auth-Token: {tokenId}
```

<a id="modify-flow-log-logger-request"></a>
#### Request

| Name | Type | Format | Required | Description |
| --- | --- | --- | --- | --- |
| tokenId | Header | String | O | Token ID |
| flowlogLoggerId | URL | UUID | O | Flow Log logger ID |
| flowlog_logger | Body | Object | O | Flow Log logger information object |
| flowlog_logger.name | Body | String | O | Flow Log logger name |
| flowlog_logger.admin_state_up | Body | Boolean |  | Enabled status of the Flow Log logger. If `false`, it is disabled and not collected. The default value is `true`. |
| flowlog_logger.description | Body | String |  | Description of the Flow Log logger |

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

<a id="modify-flow-log-logger-response"></a>
#### Response

| Name | Type | Format | Description |
| ---- | --- | ---- | --- |
| flowlog_logger | Body | Object | Flow Log logger information object |
| flowlog_logger.name | Body | String | Flow Log logger name |
| flowlog_logger.resource_type | Body | String | The target resource type of the Flow Log logger. One of the `VPC`, `SUBNET`, or `PORT`. |
| flowlog_logger.resource_id | Body | UUID | Flow Log logger's target resource ID |
| flowlog_logger.filter | Body | String | Filter to collect in Flow Log logger. One of the `ALL`, `ACCEPT`, or `DROP`. <br>* `ACCEPT` captures only packets that are allowed to communicate<br>* `DROP` captures only packets that are blocked to communicate<br>* `ALL` captures both allowed and blocked packets |
| flowlog_logger.aggregation_interval | Body | Integer | The interval at which data collected by the Flow Log logger is aggregated and written as a file to the storage. The unit is minutes. Files are created in the storage at this interval.  |
| flowlog_logger.connection_setup_only | Body | Boolean | If the value is `true`, only the packets attempting to establish the connection are collected. Setting this to `true` limits the collection targets as follows:<br>\* For TCP, packets are no longer collected when the TCP state is established<br>\* For UDP/ICMP, response packets are not collected |
| flowlog_logger.storage_type | Body | String | The storage type of the Flow Log logger. Currently, only `OBS` is supported. |
| flowlog_logger.storage_url | Body | String | Storage address of the Flow Log logger |
| flowlog_logger.log_format | Body | String | The file format that the Flow Log logger will store. The file format can be `CSV` or `PARQUET`. |
| flowlog_logger.compression_type | Body | String | Compression format of files stored by the Flow Log logger. Compressed formats `RAW` and `GZIP` are available. |
| flowlog_logger.customized_field | Body | String | Fields that the Flow Log logger records in a file. This feature is not currently supported. <br>\* For the fields supported by Flow Log, see the Fields section under Statistical Information in the Flow Log Overview of the user guide. |
| flowlog_logger.partition_period | Body | String | Refers to the folder creation structure when the Flow Log logger stores files in the storage. Supports `HOUR` and `DAY`. <br>\* If you specify `DAY`, a folder `#{year}/#{month}/#{day}` is created under the directory-path of storage_url entered by the user to separate the day<br>\* If you specify `HOUR`, a folder up to `#{year}/#{month}/#{day}/#{hour}` is created under the directory-path of storage_url entered by the user and separated by time <br> \* For other custom formats, the time is entered in #{year}, #{month}, #{day}, and #{hour}.|
| flowlog_logger.customized_file_name | Body | String | The format of the file title when the Flow Log logger stores a file in a storage. <br> \* The default value is #{logger_id}_#{year}-#{month}-#{day}T#{hour}:#{minute}:#{second}KST |
| flowlog_logger.admin_state_up | Body | Boolean | The enabled status of the Flow Log logger. If `false`, it is disabled and not collected. |
| flowlog_logger.description | Body | String | Description of the Flow Log logger |
| flowlog_logger.status | Body | Enum | Status of  the Flow Log logger |
| flowlog_logger.created_at | Body | Date | Time the Flow Log logger was created |
| flowlog_logger.updated_at | Body | Date | Time the Flow Log logger was modified |
| flowlog_logger.error_type | Body | String | If an error occurs in the Flow Log logger, the reason for the error is displayed. <br>For more information, see the error types at the bottom of the page.|

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

<a id="delete-flow-log-logger"></a>
### Delete Flow Log Logger { #delete-flow-log-logger }

```
DELETE /v2.0/flowlog-loggers/{flowlogLoggerId}
X-Auth-Token: {tokenId}
```

<a id="delete-flow-log-logger-request"></a>
#### Request

This API does not require a request body.

| Name | Type | Format | Required | Description |
| --- | --- | --- | --- | --- |
| tokenId | Header | String | O | Token ID |
| flowlogLoggerId | URL | UUID | O | Flow Log logger ID |

<a id="delete-flow-log-logger-response"></a>
#### Response

This API does not return a response body.

<br>
<br>
<br>


<a id="flow-log-logging-port"></a>
## Flow Log Logging Port { #flow-log-logging-port }

* A Flow Log logging port refers to the port that a Flow Log logger actually captures. If the resource_type of the Flow Log logger is VPC or Subnet, a single Flow Log logger manages multiple Flow Log logging ports.
* When a user creates or deletes a logger, Flow Log internally checks the ports belonging to that logger and adds or removes them as logging port targets. Therefore, users do not need to manually add or remove logging ports.
* Flow Log logging port only provides Query API.

<a id="list-flow-log-logging-ports"></a>
### List Flow Log Logging Ports { #list-flow-log-logging-ports }

```
GET /v2.0/flowlog-logging-ports
X-Auth-Token: {tokenId}
```

<a id="list-flow-log-logging-ports-request"></a>
#### Request

This API does not require a request body.

| Name | Type | Format | Required | Description |
| --- | --- | --- | --- | --- |
| tokenId | Header | String | O | Token ID |
| id | Query | UUID | - | ID of the Flow Log logging port to query |
| logger_id | Query | UUID | - | Logger ID of the Flow Log logging port to query |
| port_id | Query | UUID | - | ID of port of the Flow Log logging port to query |
| network_id | Query | UUID | - | VPC ID of the Flow Log to query |

<a id="list-flow-log-logging-ports-response"></a>
#### Response

| Name | Type | Format | Description |
| --- | --- | --- | --- |
| flowlog_logging_ports | Body | Array | Flow Log logging object list |
| flowlog_logging_ports.id | Body | UUID | Flow Log logging port ID |
| flowlog_logging_ports.logger_id | Body | UUID | ID of the Flow Log logger to which the Flow Log logging port belongs |
| flowlog_logging_ports.port_id | Body | UUID | ID of port being logged |
| flowlog_logging_ports.network_id | Body | UUID | Network ID |
| flowlog_logging_ports.created_at | Body | Date | Time the Flow Log logging port was created |
| flowlog_logging_ports.updated_at | Body | Date | Time the Flow Log logging port was modified |

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

<a id="view-flow-log-logging-port"></a>
### View Flow Log Logging Port { #view-flow-log-logging-port }

```
GET /v2.0/flowlog-logging-ports/{flowlogLoggingPortId}
X-Auth-Token: {tokenId}
```

<a id="view-flow-log-logging-port-request"></a>
#### Request

This API does not require a request body.

| Name | Type | Format | Required | Description |
| --- | --- | --- | --- | --- |
| tokenId | Header | String | O | Token ID |
| flowlogLoggingPortId | URL | UUID | O | Flow Log logging port ID |

<a id="view-flow-log-logging-port-response"></a>
#### Response

| Name | Type | Format | Description |
| --- | --- | --- | --- |
| flowlog_logging_port | Body | Object | Flow Log logging object |
| flowlog_logging_port.id | Body | UUID | Flow Log logging port ID |
| flowlog_logging_port.logger_id | Body | UUID | ID of the Flow Log logger to which the Flow Log logging port belongs |
| flowlog_logging_port.port_id | Body | UUID | ID of port being logged |
| flowlog_logging_port.network_id | Body | UUID | Network ID |
| flowlog_logging_port.created_at | Body | Date | Time the Flow Log logging port was created |
| flowlog_logging_port.updated_at | Body | Date | Time the Flow Log logging port was modified |

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

<a id="error-type"></a>
## Error Type { #error-type }

If the environment to use Flow Log is not configured correctly, an error may occur. In this case, you can look up `flowlog_logger.error_type` to find the cause of the error.


The status and error types of Flow Log loggers are as follows:

| Flow Log logger status | Error Type | Error cause | Action required |
| :---: | --- | --- | --- |
| ACTIVE | - | - | - |
| BUILD | - | - | - |
| ERROR | AuthenticationSystemError | There's a problem with the authentication system. Please contact Customer Support. | Flow Log system account has not received a token issued from the Keystone server. |
| ERROR | OBSConfigurationError | Check the OBS URL and access policy. | Dummy data was sent to the user's storage, but a 403 error occurred due to insufficient OBS access permissions. Check the container URL and access policy. |
| ERROR | OBSServiceNotAvailableError | The OBS service is not working. Please contact Customer Support. | Dummy data was sent to the user's storage, but an error other than 401 or 403 occurred. |


