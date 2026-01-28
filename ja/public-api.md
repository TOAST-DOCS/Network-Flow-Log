## Network > Flow Log > API v2ガイド

APIを使用するには、APIエンドポイントとトークンなどが必要です。[API使用準備](/Compute/Compute/ko/identity-api/)を参照して、API使用に必要な情報を準備します。

ロガーとロギングポートAPIは`network`タイプのエンドポイントを利用します。正確なエンドポイントは、トークン発行応答の`serviceCatalog`を参照してください。

| タイプ | リージョン | エンドポイント |
| --- | --- | ----- |
| network | 韓国(パンギョ)リージョン<br>韓国(ピョンチョン)リージョン<br>韓国(光州)リージョン | [https://kr1-api-network-infrastructure.nhncloudservice.com](https://kr1-api-network-infrastructure.nhncloudservice.com)<br>[https://kr2-api-network-infrastructure.nhncloudservice.com](https://kr2-api-network-infrastructure.nhncloudservice.com)<br>[https://kr3-api-network-infrastructure.nhncloudservice.com](https://kr3-api-network-infrastructure.nhncloudservice.com) |

APIレスポンスにガイドに記載されていないフィールドが表示される場合があります。このようなフィールドは、NHN Cloudの内部用途で使用し、予告なく変更される可能性があるため、使用しないでください。


## フローログロガー

### フローログロガーリストを見る

```
GET /v2.0/flowlog-loggers
X-Auth-Token: {tokenId} 
```

#### リクエスト

このAPIはリクエスト本文を要求しません。

| 名前 | 種類 | 形式 | 必須 | 説明 |
| --- | --- | --- | --- | --- |
| tokenId | Header | String | O | トークンID |
| id | Query | UUID | - | 照会するフローログロガーID |
| name | Query | String | - | 照会するフローログロガーの名前 |
| resource_type | Query | String | - | 照会するフローログロガーのリソースタイプ |
| resource_id | Query | String | - | 照会するフローログロガーのリソースID |
| filter | Query | String | - | 照会するフローログロガーのフィルタ |
| aggregation_interval | Query | Integer | - | 照会するフローログロガーの集計間隔 |
| storage_type | Query | String | - | 照会するフローログロガーのストレージタイプ |
| log_format | Query | String | - | 照会するフローログロガーの保存フォーマット |
| compression_type | Query | String | - | 照会するフローログロガーの圧縮タイプ |
| partitioned_period | Query | String | - | 照会するフローログロガーのパーティション周期 |
| status | Query | String | - | 照会するフローログロガーの状態 |

#### レスポンス

| 名前 | 種類 | 形式 | 説明 |
| --- | --- | --- | --- |
| flowlog\_loggers | Body | Array | フローログオブジェクトリスト |
| flowlog\_loggers.name | Body | String | フローログロガーの名前 |
| flowlog\_loggers.resource\_type | Body | String | フローログロガーの収集対象リソースタイプ。 `VPC`, `SUBNET`, `PORT`のいずれか |
| flowlog\_loggers.resource\_id | Body | UUID | フローログロガーの収集対象リソースID |
| flowlog\_loggers.filter | Body | String | フローログロガーの収集対象フィルタ。 `ALL`, `ACCEPT`, `DROP`のいずれか<br>\* `ACCEPT`は通信が許可されたパケットのみをキャプチャ<br>\* `DROP`は通信がブロックされたパケットのみをキャプチャ<br>\* `ALL`は通信が許可・ブロックされたパケットをすべてキャプチャ |
| flowlog\_loggers.aggregation\_interval | Body | Integer | フローログロガーが収集したデータを合算・集計してストレージにファイルとして記録する周期。単位は分。ストレージにファイルが該当値を周期で作成される。  |
| flowlog\_loggers.connection\_setup\_only | Body | Boolean | この値が`true`の場合、接続確立を試みたパケットのみを収集。true`に設定すると、以下のように収集対象が限定されます。<br>\* TCPの場合、TCP stateが確立されている場合は収集しない。<br>\* UDP/ICMPの場合はレスポンスパケットを収集しない |
| flowlog\_loggers.storage\_type | Body | Boolean | フローログロガーのストレージタイプ。現在は`OBS`のみサポート。 |
| flowlog\_loggers.storage\_url | Body | Boolean | フローログロガーのストレージアドレス |
| flowlog\_loggers.log\_format | Body | Boolean | フローログロガーが保存するファイルのフォーマット。 `CSV`, `PARQUET`ファイル形式可能。 |
| flowlog\_loggers.compression\_type | Body | Boolean | フローログロガーが保存するファイルの圧縮形式。`RAW`, `GZIP`圧縮形式可能。現在は`RAW`のみサポート。  |
| flowlog\_loggers.customized_field | Body | String | フローログロガーがファイルに記録するフィールド。現在はサポートしない機能。 |
| flowlog\_loggers.partition\_period | Body | Boolean | フローログロガーがストレージにファイルを保存する際、フォルダ作成構造を意味する。`HOUR`と`DAY`をサポート。 <br>\* `DAY`を指定すると、ユーザーが入力したstorage\_urlのdirectory-pathの下に`{YEAR}/{MONTH}/{DAY}`フォルダを作成して日付を区切る<br>\* `HOUR`を指定すると、ユーザーが入力したstorage\_urlのdirectory-pathの下に`{YEAR}/{MONTH}/{DAY}/{HOUR}`までフォルダを作成して時間別に区切る |
| flowlog\_loggers.admin\_state\_up | Body | Boolean | フローログロガーの有効化状態。 `false`の場合、無効になり収集しない。|
| flowlog\_loggers.description | Body | Boolean | フローログロガーの説明 |
| flowlog\_loggers.status | Body | Enum | フローログロガーの状態 |
| flowlog\_loggers.created_at | Body | Date | フローログロガーを作成した時間 |
| flowlog\_loggers.updated_at | Body | Date | フローログロガーが修正された時間 |
| flowlog\_loggers.error_type | Body | String | フローログロガーにエラーが発生した場合、エラー理由を表示。<br>詳細はページ下部のエラータイプをご確認ください。|

<details>
  <summary>例</summary>

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

### フローログロガー表示

```
GET /v2.0/flowlog-loggers/{flowlogLoggerId}
X-Auth-Token: {tokenId}
```

#### リクエスト

このAPIはリクエスト本文を要求しません。

| 名前 | 種類 | 形式 | 必須 | 説明 |
| --- | --- | --- | --- | --- |
| tokenId | Header | String | O | トークンID |
| flowlogLoggerId | URL | UUID | O | フローログロガーID |

#### レスポンス

| Name | In | Type | Description |
| ---- | --- | ---- | ----------- |
| flowlog\_logger | Body | Object | フローログロガー情報オブジェクト |
| flowlog\_logger.name | Body | String | フローログロガーの名前 |
| flowlog\_logger.resource\_type | Body | String | フローログロガーの収集対象リソースタイプ。 `VPC`, `SUBNET`, `PORT`のいずれか |
| flowlog\_logger.resource\_id | Body | UUID | フローログロガーの収集対象リソースID |
| flowlog\_logger.filter | Body | String | フローログロガーの収集対象フィルタ。 `ALL`, `ACCEPT`, `DROP`のいずれか<br>\* `ACCEPT`は通信が許可されたパケットのみをキャプチャ<br>\* `DROP`は通信がブロックされたパケットのみをキャプチャ<br>\* `ALL`は通信が許可・ブロックされたパケットをすべてキャプチャ |
| flowlog\_logger.aggregation\_interval | Body | Integer | フローログロガーが収集したデータを合算・集計してストレージにファイルとして記録する周期。単位は分。ストレージにファイルが該当値を周期で作成される。  |
| flowlog\_logger.connection\_setup\_only | Body | Boolean | この値が`true`の場合、接続確立を試みたパケットのみを収集。true`に設定すると、以下のように収集対象が限定されます。<br>\* TCPの場合、TCP stateが確立されている場合は収集しない。<br>\* UDP/ICMPの場合はレスポンスパケットを収集しない |
| flowlog\_logger.storage\_type | Body | Boolean | フローログロガーのストレージタイプ。現在は`OBS`のみサポート。 |
| flowlog\_logger.storage\_url | Body | Boolean | フローログロガーのストレージアドレス |
| flowlog\_logger.log\_format | Body | Boolean | フローログロガーが保存するファイルのフォーマット。 `CSV`, `PARQUET`ファイル形式可能。 |
| flowlog\_logger.compression\_type | Body | Boolean | フローログロガーが保存するファイルの圧縮形式。`RAW`, `GZIP`圧縮形式可能。現在は`RAW`のみサポート。  |
| flowlog\_logger.customized_field | Body | String | フローログロガーがファイルに記録するフィールド。現在はサポートしない機能。 |
| flowlog\_logger.partition\_period | Body | Boolean | フローログロガーがストレージにファイルを保存する際、フォルダ作成構造を意味する。`HOUR`と`DAY`をサポート。 <br>\* `DAY`を指定すると、ユーザーが入力したstorage\_urlのdirectory-pathの下に`{YEAR}/{MONTH}/{DAY}`フォルダを作成して日付を区切る<br>\* `HOUR`を指定すると、ユーザーが入力したstorage\_urlのdirectory-pathの下に`{YEAR}/{MONTH}/{DAY}/{HOUR}`までフォルダを作成して時間別に区切る |
| flowlog\_logger.admin\_state\_up | Body | Boolean | フローログロガーの有効化状態。 `false`の場合、無効になり収集しない。|
| flowlog\_logger.description | Body | Boolean | フローログロガーの説明 |
| flowlog\_logger.status | Body | Enum | フローログロガーの状態 |
| flowlog\_logger.created_at | Body | Date | フローログロガーを作成した時間 |
| flowlog\_logger.updated_at | Body | Date | フローログロガーが修正された時間 |
| flowlog\_logger.error_type | Body | String | フローログロガーにエラーが発生した場合、エラーの理由を表示。<br>詳細はページ下部のエラータイプをご確認ください。 |

<details>
  <summary>例</summary>

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

string JP

### フローログロガーを作成する

```
POST /v2.0/flowlog-loggers
X-Auth-Token: {tokenId} 
```

#### リクエスト

| 名前 | 種類 | 形式 | 必須 | 説明 |
| --- | --- | --- | --- | --- |
| tokenId | Header | String | O | トークンID |
| flowlog\_logger | Body | Object | O | フローログロガー情報オブジェクト |
| flowlog\_logger.name | Body | String | O | フローログロガーの名前 |
| flowlog\_logger.resource\_type | Body | String |  | フローログロガーの収集対象リソースタイプ。 `VPC`, `SUBNET`, `PORT`のいずれか。入力しない場合は`PORT`とみなす。 |
| flowlog\_logger.resource\_id | Body | UUID | O | フローログロガーの収集対象リソースID |
| flowlog\_logger.filter | Body | String | フローログロガーの収集対象フィルタ。 `ALL`, `ACCEPT`, `DROP`のいずれかデフォルト値は`ALL`。<br>\* `ACCEPT`は通信が許可されたパケットのみをキャプチャ<br>\* `DROP`は通信がブロックされたパケットのみをキャプチャ<br>\* `ALL`は通信が許可・ブロックされたパケットをすべてキャプチャ |
| flowlog\_logger.aggregation\_interval | Body | Integer |  | フローログロガーが収集したデータを合算・集計してストレージにファイルとして記録する周期。単位は分。ストレージにファイルが該当値を周期で作成される。デフォルト値は10分。|
| flowlog\_logger.connection\_setup\_only | Body | Boolean | この値が`true`の場合、接続確立を試みたパケットのみを収集。true`に設定すると、以下のように収集対象が限定されます。デフォルト値は`false`<br>\* TCPの場合、TCP stateが確立されている場合は収集しない。<br>\* UDP/ICMPの場合はレスポンスパケットを収集しない |
| flowlog\_logger.storage\_type | Body | Boolean | O | フローログロガーのストレージタイプ。現在は`OBS`のみサポート。 |
| flowlog\_logger.storage\_url | Body | Boolean | O | フローログロガーのストレージアドレス。ストレージタイプが`OBS`の場合は`https://{object-storage-endpoint}/{AUTH-id}/{container}/{directory-path}`をすべて入力する必要がある。|
| flowlog\_logger.log\_format | Body | Boolean |  | フローログロガーが保存するファイルのフォーマット。 `CSV`, `PARQUET`ファイル形式可能。デフォルト値は`CSV`。 |
| flowlog\_logger.compression\_type | Body | Boolean |  | フローログロガーが保存するファイルの圧縮形式。`RAW`, `GZIP`圧縮形式可能。現在は`RAW`のみサポート。デフォルト値は`RAW`。 |
| flowlog\_logger.customized_field | Body | String |  | フローログロガーがファイルに記録するフィールド |
フローログロガーがストレージにファイルを保存する際、フォルダ作成構造を意味する。`HOUR`と`DAY`をサポート。デフォルト値は`DAY`。<br>\* `DAY`を指定すると、ユーザーが入力したstorage\_urlのdirectory-pathの下に`{YEAR}/{MONTH}/{DAY}`フォルダを作成して日付を区切る<br>\* `HOUR`を指定すると、ユーザーが入力したstorage\_urlのdirectory-pathの下に`{YEAR}/{MONTH}/{DAY}/{HOUR}`までフォルダを作成して時間別に区切る |
| flowlog\_logger.admin\_state\_up | Body | Boolean |  | フローログロガーの有効化状態。 `false`の場合、無効になり収集しない。デフォルト値は`true`。|
| flowlog\_logger.description | Body | Boolean |  | フローログロガーの説明 |

<details>
  <summary>例</summary>

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

#### レスポンス

| 名前 | 種類 | 形式 | 説明 |
| ---- | --- | ---- | --- |
| flowlog\_logger | Body | Object | フローログロガー情報オブジェクト |
| flowlog\_logger.name | Body | String | フローログロガーの名前 |
| flowlog\_logger.resource\_type | Body | String | フローログロガーの収集対象リソースタイプ。 `VPC`, `SUBNET`, `PORT`のいずれか |
| flowlog\_logger.resource\_id | Body | UUID | フローログロガーの収集対象リソースID |
| flowlog\_logger.filter | Body | String | フローログロガーの収集対象フィルタ。 `ALL`, `ACCEPT`, `DROP`のいずれか<br>\* `ACCEPT`は通信が許可されたパケットのみをキャプチャ<br>\* `DROP`は通信がブロックされたパケットのみをキャプチャ<br>\* `ALL`は通信が許可・ブロックされたパケットをすべてキャプチャ |
| flowlog\_logger.aggregation\_interval | Body | Integer | フローログロガーが収集したデータを合算・集計してストレージにファイルとして記録する周期。単位は分。ストレージにファイルが該当値を周期で作成される。  |
| flowlog\_logger.connection\_setup\_only | Body | Boolean | この値が`true`の場合、接続確立を試みたパケットのみを収集。true`に設定すると、以下のように収集対象が限定されます。<br>\* TCPの場合、TCP stateが確立されている場合は収集しない。<br>\* UDP/ICMPの場合はレスポンスパケットを収集しない |
| flowlog\_logger.storage\_type | Body | Boolean | フローログロガーのストレージタイプ。現在は`OBS`のみサポート。 |
| flowlog\_logger.storage\_url | Body | Boolean | フローログロガーのストレージアドレス |
| flowlog\_logger.log\_format | Body | Boolean | フローログロガーが保存するファイルのフォーマット。 `CSV`, `PARQUET`ファイル形式可能。 |
| flowlog\_logger.compression\_type | Body | Boolean | フローログロガーが保存するファイルの圧縮形式。`RAW`, `GZIP`圧縮形式可能。現在は`RAW`のみサポート。  |
| flowlog\_logger.customized_field | Body | String | フローログロガーがファイルに記録するフィールド。現在はサポートしない機能。 |
| flowlog\_logger.partition\_period | Body | Boolean | フローログロガーがストレージにファイルを保存する際、フォルダ作成構造を意味する。`HOUR`と`DAY`をサポート。 <br>\* `DAY`を指定すると、ユーザーが入力したstorage\_urlのdirectory-pathの下に`{YEAR}/{MONTH}/{DAY}`フォルダを作成して日付を区切る<br>\* `HOUR`を指定すると、ユーザーが入力したstorage\_urlのdirectory-pathの下に`{YEAR}/{MONTH}/{DAY}/{HOUR}`までフォルダを作成して時間別に区切る |
| flowlog\_logger.admin\_state\_up | Body | Boolean | フローログロガーの有効化状態。 `false`の場合、無効になり収集しない。|
| flowlog\_logger.description | Body | Boolean | フローログロガーの説明 |
| flowlog\_logger.status | Body | Enum | フローログロガーの状態 |
| flowlog\_logger.created_at | Body | Date | フローログロガーを作成した時間 |
| flowlog\_logger.updated_at | Body | Date | フローログロガーが修正された時間 |
| flowlog\_logger.error_type | Body | String | フローログロガーにエラーが発生した場合、エラー理由を表示。<br>詳細は、ページ下部をご確認ください。|

<details>
  <summary>例</summary>

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

string JP

### フローログロガーを修正する

```
PUT /v2.0/flowlog-loggers/{flowlogLoggerId}
X-Auth-Token: {tokenId} 
```

#### リクエスト

| 名前 | 種類 | 形式 | 必須 | 説明 |
| --- | --- | --- | --- | --- |
| tokenId | Header | String | O | トークンID |
| flowlogLoggerId | URL | UUID | O | フローログロガーID |
| flowlog\_logger | Body | Object | O | フローログロガー情報オブジェクト |
| flowlog\_logger.name | Body | String | O | フローログロガーの名前 |
| flowlog\_logger.admin\_state\_up | Body | Boolean |  | フローログロガーの有効化状態。 `false`の場合、無効になり収集しない。デフォルト値は`true`。|
| flowlog\_logger.description | Body | Boolean |  | フローログロガーの説明 |

<details>
  <summary>例</summary>

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

#### レスポンス

| 名前 | 種類 | 形式 | 説明 |
| ---- | --- | ---- | --- |
| flowlog\_logger | Body | Object | フローログロガー情報オブジェクト |
| flowlog\_logger.name | Body | String | フローログロガーの名前 |
| flowlog\_logger.resource\_type | Body | String | フローログロガーの収集対象リソースタイプ。 `VPC`, `SUBNET`, `PORT`のいずれか |
| flowlog\_logger.resource\_id | Body | UUID | フローログロガーの収集対象リソースID |
| flowlog\_logger.filter | Body | String | フローログロガーの収集対象フィルタ。 `ALL`, `ACCEPT`, `DROP`のいずれか<br>\* `ACCEPT`は通信が許可されたパケットのみをキャプチャ<br>\* `DROP`は通信がブロックされたパケットのみをキャプチャ<br>\* `ALL`は通信が許可・ブロックされたパケットをすべてキャプチャ |
| flowlog\_logger.aggregation\_interval | Body | Integer | フローログロガーが収集したデータを合算・集計してストレージにファイルとして記録する周期。単位は分。ストレージにファイルが該当値を周期で作成される。  |
| flowlog\_logger.connection\_setup\_only | Body | Boolean | この値が`true`の場合、接続確立を試みたパケットのみを収集。true`に設定すると、以下のように収集対象が限定されます。<br>\* TCPの場合、TCP stateが確立されている場合は収集しない。<br>\* UDP/ICMPの場合はレスポンスパケットを収集しない |
| flowlog\_logger.storage\_type | Body | Boolean | フローログロガーのストレージタイプ。現在は`OBS`のみサポート。 |
| flowlog\_logger.storage\_url | Body | Boolean | フローログロガーのストレージアドレス |
| flowlog\_logger.log\_format | Body | Boolean | フローログロガーが保存するファイルのフォーマット。 `CSV`, `PARQUET`ファイル形式可能。 |
| flowlog\_logger.compression\_type | Body | Boolean | フローログロガーが保存するファイルの圧縮形式。`RAW`, `GZIP`圧縮形式可能。現在は`RAW`のみサポート。  |
| flowlog\_logger.customized_field | Body | String | フローログロガーがファイルに記録するフィールド。現在はサポートしない機能。 |
| flowlog\_logger.partition\_period | Body | Boolean | フローログロガーがストレージにファイルを保存する際、フォルダ作成構造を意味する。`HOUR`と`DAY`をサポート。 <br>\* `DAY`を指定すると、ユーザーが入力したstorage\_urlのdirectory-pathの下に`{YEAR}/{MONTH}/{DAY}`フォルダを作成して日付を区切る<br>\* `HOUR`を指定すると、ユーザーが入力したstorage\_urlのdirectory-pathの下に`{YEAR}/{MONTH}/{DAY}/{HOUR}`までフォルダを作成して時間別に区切る |
| flowlog\_logger.admin\_state\_up | Body | Boolean | フローログロガーの有効化状態。 `false`の場合、無効になり収集しない。|
| flowlog\_logger.description | Body | Boolean | フローログロガーの説明 |
| flowlog\_logger.status | Body | Enum | フローログロガーの状態 |
| flowlog\_logger.created_at | Body | Date | フローログロガーを作成した時間 |
| flowlog\_logger.updated_at | Body | Date | フローログロガーが修正された時間 |
| flowlog\_logger.error_type | Body | String | フローログロガーにエラーが発生した場合、エラー理由を表示。<br>詳細は、ページ下部をご確認ください。|

<details>
  <summary>例</summary>

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

string JP

### フローログロガーを削除する

```
DELETE /v2.0/flowlog-loggers/{flowlogLoggerId}
X-Auth-Token: {tokenId} 
```

#### リクエスト

このAPIはリクエスト本文を要求しません。

| 名前 | 種類 | 形式 | 必須 | 説明 |
| --- | --- | --- | --- | --- |
| tokenId | Header | String | O | トークンID |
| flowlogLoggerId | URL | UUID | O | フローログロガーID |

#### レスポンス

このAPIはレスポンス本文を返しません。

<br>
<br>
<br>


## フローログロギングポート

* フローログロギングポートとは、フローログロガーが実質的にキャプチャするポートを意味します。フローロガーのresource\_typeがVPCまたはSubnetの場合、1つのフローログロガーが複数のフローログロギングポートを管理することになります。
* ユーザーがロガーを作成または削除する際、フローログは内部的にそのロガーに属しているポートを確認し、ロギングポート対象として追加または削除を行います。したがって、ユーザーが別途にロギングポートを追加/削除する必要はありません。
* フローログのロギングポートは、照会APIのみを提供します。

### フローログロギングポートリスト表示

```
GET /v2.0/flowlog-logging-ports
X-Auth-Token: {tokenId} 
```

#### リクエスト

このAPIはリクエスト本文を要求しません。

| 名前 | 種類 | 形式 | 必須 | 説明 |
| --- | --- | --- | --- | --- |
| tokenId | Header | String | O | トークンID |
| id | Query | UUID | - | 照会するフローログロギングポートのID |
| logger_id | Query | UUID | - | 照会するフローログロギングポートのロガーID |
| port_id | Query | UUID | - | 照会するフローログロギングポートのポートID |
| network_id | Query | UUID | - | 照会するフローログのVPC ID |

#### レスポンス

| 名前 | 種類 | 形式 | 説明 |
| --- | --- | --- | --- |
| flowlog\_logging\_ports | Array | Object | フローログロギングオブジェクトの一覧 |
| flowlog\_logging\_ports.id | Body | UUID | フローログロギングポートID |
| flowlog\_logging\_ports.logger_id | Body | UUID | 該当フローログロギングポートが属するフローログロガーのID |
| flowlog\_logging\_ports.port\_id | Body | UUID | ロギングしているポートのID |
| flowlog\_logging\_ports.network\_id | Body | UUID | ネットワークID |
| flowlog\_logging\_ports.created\_at | Body | Date | フローログロギングポートが作成された時間 |
| flowlog\_logging\_ports.updated\_at | Body | Date | フローログロギングポートが修正された時間 |

<details>
  <summary>例</summary>

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

string JP

### フローログロギングポート表示

```
GET /v2.0/flowlog-logging-ports/{flowlogLoggingPortId}
X-Auth-Token: {tokenId} 
```

#### リクエスト

このAPIはリクエスト本文を要求しません。

| 名前 | 種類 | 形式 | 必須 | 説明 |
| --- | --- | --- | --- | --- |
| tokenId | Header | String | O | トークンID |
| flowlogLoggingPortId | URL | UUID | O | フローログロギングポートID |

#### レスポンス

| 名前 | 種類 | 形式 | 説明 |
| --- | --- | --- | --- |
| flowlog\_logging\_port | Body | Object | フローログロギングオブジェクト |
| flowlog\_logging\_port.id | Body | UUID | フローログロギングポートID |
| flowlog\_logging\_port.logger_id | Body | UUID | 該当フローログロギングポートが属するフローログロガーのID |
| flowlog\_logging\_port.port\_id | Body | UUID | ロギングしているポートのID |
| flowlog\_logging\_port.network\_id | Body | UUID | ネットワークID |
| flowlog\_logging\_port.created\_at | Body | Date | フローログロギングポートが作成された時間 |
| flowlog\_logging\_port.updated\_at | Body | Date | フローログロギングポートが修正された時間 |

<details>
  <summary>例</summary>

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

## エラータイプ

フローログを使う環境が正しく設定されていない場合、エラーが発生することがあります。この場合、flowlog_logger.error_typeを照会してエラーの原因を確認できます。


フローログロガーの状態とエラータイプは次のとおりです。

| フローログロガー状態 | エラータイプ | エラー原因 | 確認必要事項 |
| :---: | --- | --- | --- |
| ACTIVE | - | - | - | - |
| BUILD | - | - | - | - |
| ERROR | AuthenticationSystemError | 認証システムに問題があります。サポートにお問い合わせください。 | フローログシステムアカウントがKeystoneサーバーからトークン発行を受けられなかった場合です。 |
| ERROR | OBSConfigurationError | OBS URL及びアクセスポリシーをご確認ください。 | ユーザーのストレージにダミーデータを送信したが、OBSアクセス権がないため403エラーが発生した場合です。コンテナURL及びアクセスポリシーをご確認ください。 |
| ERROR | OBSServiceNotAvailableError | OBSサービスが動作しません。サポートにお問い合わせください。| ユーザーのストレージにダミーデータを送信したが、401、403以外のエラーが発生した場合です。|
