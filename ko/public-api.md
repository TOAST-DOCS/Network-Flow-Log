## Network > Flow Log > API v2 가이드

API를 사용하려면 API 엔드포인트와 토큰 등이 필요합니다. [API 사용 준비](/Compute/Compute/ko/identity-api/)를 참고하여 API 사용에 필요한 정보를 준비합니다.

로거와 로깅 포트 API는 `network` 타입 엔드포인트를 이용합니다. 정확한 엔드포인트는 토큰 발급 응답의 `serviceCatalog`를 참조합니다.

| 타입 | 리전 | 엔드포인트 |
| --- | --- | ----- |
| network | 한국(판교) 리전<br>한국(평촌) 리전<br>한국(광주) 리전 | [https://kr1-api-network-infrastructure.nhncloudservice.com](https://kr1-api-network-infrastructure.nhncloudservice.com)<br>[https://kr2-api-network-infrastructure.nhncloudservice.com](https://kr2-api-network-infrastructure.nhncloudservice.com)<br>[https://kr3-api-network-infrastructure.nhncloudservice.com](https://kr3-api-network-infrastructure.nhncloudservice.com) |

API 응답에 가이드에 명시되지 않은 필드가 나타날 수 있습니다. 이런 필드는 NHN Cloud 내부 용도로 사용하며 사전 공지 없이 변경될 수 있으므로 사용하지 않습니다.


## 플로우 로그 로거

### 플로우 로그 로거 목록 보기

```
GET /v2.0/flowlog-loggers
X-Auth-Token: {tokenId} 
```

#### 요청

이 API는 요청 본문을 요구하지 않습니다.

| 이름 | 종류 | 형식 | 필수 | 설명 |
| --- | --- | --- | --- | --- |
| tokenId | Header | String | O | 토큰 ID |
| id | Query | UUID | - | 조회할 플로우 로그 로거 ID |
| name | Query | String | - | 조회할 플로우 로그 로거 이름 |
| resource_type | Query | String | - | 조회할 플로우 로그 로거의 리소스 타입 |
| resource_id | Query | String | - | 조회할 플로우 로그 로거의 리소스 ID |
| filter | Query | String | - |  조회할 플로우 로그 로거의 필터 |
| aggregation_interval | Query | Integer | - |  조회할 플로우 로그 로거의 집계 간격 |
| storage_type | Query | String | - |  조회할 플로우 로그 로거의 저장소 타입 |
| log_format | Query | String | - |  조회할 플로우 로그 로거의 저장 형식 |
| compression_type | Query | String | - |  조회할 플로우 로그 로거의 압축 타입 |
| partitioned_period | Query | String | - | 조회할 플로우 로그 로거의 파티션 주기  |
| customized_file_name | Query | String | - | 조회할 플로우 로그 로거의 파일 제목 형식 |
| status | Query | String | - | 조회할 플로우 로그 로거의 상태 |

#### 응답

| 이름 | 종류 | 형식 | 설명 |
| --- | --- | --- | --- |
| flowlog\_loggers | Body | Array | 플로우 로그 객체 목록 |
| flowlog\_loggers.name | Body | String | 플로우 로그 로거 이름 |
| flowlog\_loggers.resource\_type | Body | String | 플로우 로그 로거의 수집 대상 리소스 타입. `VPC`, `SUBNET`, `PORT` 중에 하나. |
| flowlog\_loggers.resource\_id | Body | UUID | 플로우 로그 로거의 수집 대상 리소스 ID |
| flowlog\_loggers.filter | Body | String | 플로우 로그 로거의 수집 대상 필터. `ALL`, `ACCEPT`, `DROP` 중에 하나. <br>\* `ACCEPT`은 통신이 허용된 패킷만을 캡쳐<br>\* `DROP`은 통신이 차단된 패킷만을 캡쳐<br>\* `ALL`은 통신이 허용, 차단된 패킷을 모두 캡쳐 |
| flowlog\_loggers.aggregation\_interval | Body | Integer | 플로우 로그 로거가 수집한 데이터를 합산 및 집계하여 저장소에 파일로 기록할 주기. 단위는 분. 저장소에 파일이 해당 값을 주기로 생성됨.  |
| flowlog\_loggers.connection\_setup\_only | Body | Boolean | 해당 값이 `true`라면 연결 수립을 시도한 패킷만을 수집. `true`로 설정하면 다음과 같이 수집 대상이 한정됨.<br>\* TCP의 경우 TCP state가 established인 경우는 더 이상 수집하지 않음<br>\* UDP/ICMP의 경우에는 응답 패킷을 수집하지 않음 |
| flowlog\_loggers.storage\_type | Body | String | 플로우 로그 로거의 저장소 타입. 현재는 `OBS`만 지원. |
| flowlog\_loggers.storage\_url | Body | String | 플로우 로그 로거의 저장소 주소 |
| flowlog\_loggers.log\_format | Body | String | 플로우 로그 로거가 저장할 파일 형식. `CSV`, `PARQUET` 파일 형식 가능. |
| flowlog\_loggers.compression\_type | Body | String | 플로우 로그 로거가 저장할 파일의 압축 형태. `RAW`, `GZIP` 압축 형식 가능. |
| flowlog\_loggers.customized_field | Body | String | 플로우 로그 로거가 파일에 기록할 필드. <br>\* Flow Log가 지원하는 필드는 사용자 가이드 Flow Log 개요에서 통계 제공 정보의 필드를 확인하시길 바랍니다.|
| flowlog\_loggers.partition\_period | Body | String | 플로우 로그 로거가 저장소에 파일을 저장할 때, 폴더 생성 구조를 의미. `HOUR`와 `DAY`를 지원. <br>\* `DAY`를 지정하면 사용자가 입력한 storage\_url의 directory-path 하위에 `#{year}/#{month}/#{day}` 폴더를 생성하여 일자를 구분<br>\* `HOUR`를 지정하면 사용자가 입력한 storage\_url의 directory-path 하위에 `#{year}/#{month}/#{day}/#{hour}`까지 폴더를 생성하여 시간별로 구분 <br> \* 그 외 사용자 정의 형식의 경우에는 #{year}, #{month}, #{day}, #{hour}에 시간이 기입됨|
| flowlog\_loggers.customized_file_name | Body | String | 플로우 로그 로거가 저장소에 파일을 저장할 때, 파일 제목의 형식. <br> \* 기본값은 #{logger_id}_#{year}-#{month}-#{day}T#{hour}:#{minute}:#{second}KST |
| flowlog\_loggers.admin\_state\_up | Body | Boolean | 플로우 로그 로거의 활성화 상태. `false`인 경우 비활성화되어 수집하지 않음. |
| flowlog\_loggers.description | Body | String | 플로우 로그 로거의 설명 |
| flowlog\_loggers.status | Body | Enum | 플로우 로그 로거의 상태 |
| flowlog\_loggers.created_at | Body | Date | 플로우 로그 로거를 생성한 시간 |
| flowlog\_loggers.updated_at | Body | Date | 플로우 로그 로거가 수정된 시간 |
| flowlog\_loggers.error_type | Body | String | 플로우 로그 로거에 오류가 발생한 경우, 오류 이유를 표시. <br>자세한 내용은 페이지 최하단의 오류 유형을 확인하시길 바랍니다.|

<details>
  <summary>예시</summary>

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

### 플로우 로그 로거 보기

```
GET /v2.0/flowlog-loggers/{flowlogLoggerId}
X-Auth-Token: {tokenId}
```

#### 요청

이 API는 요청 본문을 요구하지 않습니다.

| 이름 | 종류 | 형식 | 필수 | 설명 |
| --- | --- | --- | --- | --- |
| tokenId | Header | String | O | 토큰 ID |
| flowlogLoggerId | URL | UUID | O | 플로우 로그 로거 ID |

#### 응답

| Name | In | Type | Description |
| ---- | --- | ---- | ----------- |
| flowlog\_logger | Body | Object | 플로우 로그 로거 정보 객체 |
| flowlog\_logger.name | Body | String | 플로우 로그 로거 이름 |
| flowlog\_logger.resource\_type | Body | String | 플로우 로그 로거의 수집 대상 리소스 타입. `VPC`, `SUBNET`, `PORT` 중에 하나. |
| flowlog\_logger.resource\_id | Body | UUID | 플로우 로그 로거의 수집 대상 리소스 ID |
| flowlog\_logger.filter | Body | String | 플로우 로그 로거의 수집 대상 필터. `ALL`, `ACCEPT`, `DROP` 중에 하나. <br>\* `ACCEPT`은 통신이 허용된 패킷만을 캡쳐<br>\* `DROP`은 통신이 차단된 패킷만을 캡쳐<br>\* `ALL`은 통신이 허용, 차단된 패킷을 모두 캡쳐 |
| flowlog\_logger.aggregation\_interval | Body | Integer | 플로우 로그 로거가 수집한 데이터를 합산 및 집계하여 저장소에 파일로 기록할 주기. 단위는 분. 저장소에 파일이 해당 값을 주기로 생성됨.  |
| flowlog\_logger.connection\_setup\_only | Body | Boolean | 해당 값이 `true`라면 연결 수립을 시도한 패킷만을 수집. `true`로 설정하면 다음과 같이 수집 대상이 한정됨.<br>\* TCP의 경우 TCP state가 established인 경우는 더 이상 수집하지 않음<br>\* UDP/ICMP의 경우에는 응답 패킷을 수집하지 않음 |
| flowlog\_logger.storage\_type | Body | String | 플로우 로그 로거의 저장소 타입. 현재는 `OBS`만 지원. |
| flowlog\_logger.storage\_url | Body | String | 플로우 로그 로거의 저장소 주소 |
| flowlog\_logger.log\_format | Body | String | 플로우 로그 로거가 저장할 파일 형식. `CSV`, `PARQUET` 파일 형식 가능. |
| flowlog\_logger.compression\_type | Body | String | 플로우 로그 로거가 저장할 파일의 압축 형태. `RAW`, `GZIP` 압축 형식 가능. |
| flowlog\_logger.customized_field | Body | String | 플로우 로그 로거가 파일에 기록할 필드. <br>\* Flow Log가 지원하는 필드는 사용자 가이드 Flow Log 개요에서 통계 제공 정보의 필드를 확인하시길 바랍니다.|
| flowlog\_logger.partition\_period | Body | String | 플로우 로그 로거가 저장소에 파일을 저장할 때, 폴더 생성 구조를 의미. `HOUR`와 `DAY`를 지원. <br>\* `DAY`를 지정하면 사용자가 입력한 storage\_url의 directory-path 하위에 `#{year}/#{month}/#{day}` 폴더를 생성하여 일자를 구분<br>\* `HOUR`를 지정하면 사용자가 입력한 storage\_url의 directory-path 하위에 `#{year}/#{month}/#{day}/#{hour}`까지 폴더를 생성하여 시간별로 구분 <br> \* 그 외 사용자 정의 형식의 경우에는 #{year}, #{month}, #{day}, #{hour}에 시간이 기입됨|
| flowlog\_logger.customized_file_name | Body | String | 플로우 로그 로거가 저장소에 파일을 저장할 때, 파일 제목의 형식. <br> \* 기본값은 #{logger_id}_#{year}-#{month}-#{day}T#{hour}:#{minute}:#{second}KST |
| flowlog\_logger.admin\_state\_up | Body | Boolean | 플로우 로그 로거의 활성화 상태. `false`인 경우 비활성화되어 수집하지 않음. |
| flowlog\_logger.description | Body | String | 플로우 로그 로거의 설명 |
| flowlog\_logger.status | Body | Enum | 플로우 로그 로거의 상태 |
| flowlog\_logger.created_at | Body | Date | 플로우 로그 로거를 생성한 시간 |
| flowlog\_logger.updated_at | Body | Date | 플로우 로그 로거가 수정된 시간 |
| flowlog\_logger.error_type | Body | String | 플로우 로그 로거에 오류가 발생한 경우, 오류 이유를 표시.. <br>자세한 내용은 페이지 최하단의 오류 유형을 확인하시길 바랍니다. |

<details>
  <summary>예시</summary>

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

### 플로우 로그 로거 생성하기

```
POST /v2.0/flowlog-loggers
X-Auth-Token: {tokenId} 
```

#### 요청

| 이름 | 종류 | 형식 | 필수 | 설명 |
| --- | --- | --- | --- | --- |
| tokenId | Header | String | O | 토큰 ID |
| flowlog\_logger | Body | Object | O | 플로우 로그 로거 정보 객체 |
| flowlog\_logger.name | Body | String | O | 플로우 로그 로거 이름 |
| flowlog\_logger.resource\_type | Body | String |  | 플로우 로그 로거의 수집 대상 리소스 타입. `VPC`, `SUBNET`, `PORT` 중에 하나. 입력하지 않으면 `PORT`로 간주. |
| flowlog\_logger.resource\_id | Body | UUID | O | 플로우 로그 로거의 수집 대상 리소스 ID |
| flowlog\_logger.filter | Body | String |  | 플로우 로그 로거의 수집 대상 필터. `ALL`, `ACCEPT`, `DROP` 중에 하나. 기본값은 `ALL`.<br>\* `ACCEPT`은 통신이 허용된 패킷만을 캡쳐<br>\* `DROP`은 통신이 차단된 패킷만을 캡쳐<br>\* `ALL`은 통신이 허용, 차단된 패킷을 모두 캡쳐 |
| flowlog\_logger.aggregation\_interval | Body | Integer |  | 플로우 로그 로거가 수집한 데이터를 합산 및 집계하여 저장소에 파일로 기록할 주기. 단위는 분. 저장소에 파일이 해당 값을 주기로 생성됨. 기본값은 10분. |
| flowlog\_logger.connection\_setup\_only | Body | Boolean |  | 해당 값이 `true`라면 연결 수립을 시도한 패킷만을 수집. `true`로 설정하면 다음과 같이 수집 대상이 한정됨. 기본값은 `false`<br>\* TCP의 경우 TCP state가 established인 경우는 더 이상 수집하지 않음<br>\* UDP/ICMP의 경우에는 응답 패킷을 수집하지 않음 |
| flowlog\_logger.storage\_type | Body | String | O | 플로우 로그 로거의 저장소 타입. 현재는 `OBS`만 지원. |
| flowlog\_logger.storage\_url | Body | String | O | 플로우 로그 로거의 저장소 주소. 저장소 타입이 `OBS`인 경우에는 `https://{object-storage-endpoint}/{AUTH-id}/{container}/{directory-path}`를 모두 입력해야 함. |
| flowlog\_logger.log\_format | Body | String |  | 플로우 로그 로거가 저장할 파일 형식. `CSV`, `PARQUET` 파일 형식 가능. 기본값은 `CSV`. |
| flowlog\_logger.compression\_type | Body | String |  | 플로우 로그 로거가 저장할 파일의 압축 형태. `RAW`, `GZIP` 압축 형식 가능. 기본값은 `RAW`. |
| flowlog\_logger.customized_field | Body | String |  | 플로우 로그 로거가 파일에 기록할 필드, <br>\* 아래 예시와 같이 쉼표 구분 형태로 작성해야 하며, 순서의 영향을 받음 <br>\* Flow Log가 지원하는 필드는 사용자 가이드 Flow Log 개요에서 통계 제공 정보의 필드를 확인하시길 바랍니다. |
| flowlog\_logger.partition\_period | Body | String |  | 플로우 로그 로거가 저장소에 파일을 저장할 때, 폴더 생성 구조를 의미. `HOUR`와 `DAY`를 지원. <br>\* `DAY`를 지정하면 사용자가 입력한 storage\_url의 directory-path 하위에 `#{year}/#{month}/#{day}` 폴더를 생성하여 일자를 구분<br>\* `HOUR`를 지정하면 사용자가 입력한 storage\_url의 directory-path 하위에 `#{year}/#{month}/#{day}/#{hour}`까지 폴더를 생성하여 시간별로 구분 <br> \* 그 외 사용자 정의 형식의 경우에는 #{year}, #{month}, #{day}, #{hour}에 시간이 기입됨 <br> \* 사용자 정의 형식은 숫자, 영어 및 일부 특수기호 (/,-,_,:,=)만 입력이 가능. `/`를 입력하면 폴더를 구분. <br> \* e.g.) `year=#{year}/month=#{month}/day=#{day}` 를 입력하면 `year=2024/month=09/day=01` 폴더 아래에 2024년 9월 1일의 플로우 로그 파일들을 보관|
| flowlog\_logger.customized_file_name | Body | String | | 플로우 로그 로거가 저장소에 파일을 저장할 때, 파일 제목의 형식. <br> \* 기본값은 #{logger_id}_#{year}-#{month}-#{day}T#{hour}:#{minute}:#{second}KST <br> \* #{logger_id}, #{year}, #{month}, #{day}, #{hour}, #{minute}, #{second} 의 템플릿 변수들을 각각 정확히 1번씩 활용하여 플로우 로거 파일의 제목을 직접 정의할 수 있음. |
| flowlog\_logger.admin\_state\_up | Body | Boolean |  | 플로우 로그 로거의 활성화 상태. `false`인 경우 비활성화되어 수집하지 않음. 기본값은 `true`. |
| flowlog\_logger.description | Body | String |  | 플로우 로그 로거의 설명 |

<details>
  <summary>예시</summary>

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

#### 응답

| 이름 | 종류 | 형식 | 설명 |
| ---- | --- | ---- | --- |
| flowlog\_logger | Body | Object | 플로우 로그 로거 정보 객체 |
| flowlog\_logger.name | Body | String | 플로우 로그 로거 이름 |
| flowlog\_logger.resource\_type | Body | String | 플로우 로그 로거의 수집 대상 리소스 타입. `VPC`, `SUBNET`, `PORT` 중에 하나. |
| flowlog\_logger.resource\_id | Body | UUID | 플로우 로그 로거의 수집 대상 리소스 ID |
| flowlog\_logger.filter | Body | String | 플로우 로그 로거의 수집 대상 필터. `ALL`, `ACCEPT`, `DROP` 중에 하나. <br>\* `ACCEPT`은 통신이 허용된 패킷만을 캡쳐<br>\* `DROP`은 통신이 차단된 패킷만을 캡쳐<br>\* `ALL`은 통신이 허용, 차단된 패킷을 모두 캡쳐 |
| flowlog\_logger.aggregation\_interval | Body | Integer | 플로우 로그 로거가 수집한 데이터를 합산 및 집계하여 저장소에 파일로 기록할 주기. 단위는 분. 저장소에 파일이 해당 값을 주기로 생성됨.  |
| flowlog\_logger.connection\_setup\_only | Body | Boolean | 해당 값이 `true`라면 연결 수립을 시도한 패킷만을 수집. `true`로 설정하면 다음과 같이 수집 대상이 한정됨.<br>\* TCP의 경우 TCP state가 established인 경우는 더 이상 수집하지 않음<br>\* UDP/ICMP의 경우에는 응답 패킷을 수집하지 않음 |
| flowlog\_logger.storage\_type | Body | String | 플로우 로그 로거의 저장소 타입. 현재는 `OBS`만 지원. |
| flowlog\_logger.storage\_url | Body | String | 플로우 로그 로거의 저장소 주소 |
| flowlog\_logger.log\_format | Body | String | 플로우 로그 로거가 저장할 파일 형식. `CSV`, `PARQUET` 파일 형식 가능. |
| flowlog\_logger.compression\_type | Body | String | 플로우 로그 로거가 저장할 파일의 압축 형태. `RAW`, `GZIP` 압축 형식 가능. |
| flowlog\_logger.customized_field | Body | String | 플로우 로그 로거가 파일에 기록할 필드.<br>\* Flow Log가 지원하는 필드는 사용자 가이드 Flow Log 개요에서 통계 제공 정보의 필드를 확인하시길 바랍니다. |
| flowlog\_logger.partition\_period | Body | String | 플로우 로그 로거가 저장소에 파일을 저장할 때, 폴더 생성 구조를 의미. `HOUR`와 `DAY`를 지원. <br>\* `DAY`를 지정하면 사용자가 입력한 storage\_url의 directory-path 하위에 `#{year}/#{month}/#{day}` 폴더를 생성하여 일자를 구분<br>\* `HOUR`를 지정하면 사용자가 입력한 storage\_url의 directory-path 하위에 `#{year}/#{month}/#{day}/#{hour}`까지 폴더를 생성하여 시간별로 구분 <br> \* 그 외 사용자 정의 형식의 경우에는 #{year}, #{month}, #{day}, #{hour}에 시간이 기입됨|
| flowlog\_logger.customized_file_name | Body | String | 플로우 로그 로거가 저장소에 파일을 저장할 때, 파일 제목의 형식. <br> \* 기본값은 #{logger_id}_#{year}-#{month}-#{day}T#{hour}:#{minute}:#{second}KST |
| flowlog\_logger.admin\_state\_up | Body | Boolean | 플로우 로그 로거의 활성화 상태. `false`인 경우 비활성화되어 수집하지 않음. |
| flowlog\_logger.description | Body | String | 플로우 로그 로거의 설명 |
| flowlog\_logger.status | Body | Enum | 플로우 로그 로거의 상태 |
| flowlog\_logger.created_at | Body | Date | 플로우 로그 로거를 생성한 시간 |
| flowlog\_logger.updated_at | Body | Date | 플로우 로그 로거가 수정된 시간 |
| flowlog\_logger.error_type | Body | String | 플로우 로그 로거에 오류가 발생한 경우, 오류 이유를 표시. <br>자세한 내용은 페이지 최하단의 을 확인하시길 바랍니다.|

<details>
  <summary>예시</summary>

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

### 플로우 로그 로거 수정하기

```
PUT /v2.0/flowlog-loggers/{flowlogLoggerId}
X-Auth-Token: {tokenId} 
```

#### 요청

| 이름 | 종류 | 형식 | 필수 | 설명 |
| --- | --- | --- | --- | --- |
| tokenId | Header | String | O | 토큰 ID |
| flowlogLoggerId | URL | UUID | O | 플로우 로그 로거 ID |
| flowlog\_logger | Body | Object | O | 플로우 로그 로거 정보 객체 |
| flowlog\_logger.name | Body | String | O | 플로우 로그 로거 이름 |
| flowlog\_logger.admin\_state\_up | Body | Boolean |  | 플로우 로그 로거의 활성화 상태. `false`인 경우 비활성화되어 수집하지 않음. 기본값은 `true`. |
| flowlog\_logger.description | Body | String |  | 플로우 로그 로거의 설명 |

<details>
  <summary>예시</summary>

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

#### 응답

| 이름 | 종류 | 형식 | 설명 |
| ---- | --- | ---- | --- |
| flowlog\_logger | Body | Object | 플로우 로그 로거 정보 객체 |
| flowlog\_logger.name | Body | String | 플로우 로그 로거 이름 |
| flowlog\_logger.resource\_type | Body | String | 플로우 로그 로거의 수집 대상 리소스 타입. `VPC`, `SUBNET`, `PORT` 중에 하나. |
| flowlog\_logger.resource\_id | Body | UUID | 플로우 로그 로거의 수집 대상 리소스 ID |
| flowlog\_logger.filter | Body | String | 플로우 로그 로거의 수집 대상 필터. `ALL`, `ACCEPT`, `DROP` 중에 하나. <br>\* `ACCEPT`은 통신이 허용된 패킷만을 캡쳐<br>\* `DROP`은 통신이 차단된 패킷만을 캡쳐<br>\* `ALL`은 통신이 허용, 차단된 패킷을 모두 캡쳐 |
| flowlog\_logger.aggregation\_interval | Body | Integer | 플로우 로그 로거가 수집한 데이터를 합산 및 집계하여 저장소에 파일로 기록할 주기. 단위는 분. 저장소에 파일이 해당 값을 주기로 생성됨.  |
| flowlog\_logger.connection\_setup\_only | Body | Boolean | 해당 값이 `true`라면 연결 수립을 시도한 패킷만을 수집. `true`로 설정하면 다음과 같이 수집 대상이 한정됨.<br>\* TCP의 경우 TCP state가 established인 경우는 더 이상 수집하지 않음<br>\* UDP/ICMP의 경우에는 응답 패킷을 수집하지 않음 |
| flowlog\_logger.storage\_type | Body | String | 플로우 로그 로거의 저장소 타입. 현재는 `OBS`만 지원. |
| flowlog\_logger.storage\_url | Body | String | 플로우 로그 로거의 저장소 주소 |
| flowlog\_logger.log\_format | Body | String | 플로우 로그 로거가 저장할 파일 형식. `CSV`, `PARQUET` 파일 형식 가능. |
| flowlog\_logger.compression\_type | Body | String | 플로우 로그 로거가 저장할 파일의 압축 형태. `RAW`, `GZIP` 압축 형식 가능. |
| flowlog\_logger.customized_field | Body | String | 플로우 로그 로거가 파일에 기록할 필드. 현재는 지원하지 않는 기능. <br>\* Flow Log가 지원하는 필드는 사용자 가이드 Flow Log 개요에서 통계 제공 정보의 필드를 확인하시길 바랍니다. |
| flowlog\_logger.partition\_period | Body | String | 플로우 로그 로거가 저장소에 파일을 저장할 때, 폴더 생성 구조를 의미. `HOUR`와 `DAY`를 지원. <br>\* `DAY`를 지정하면 사용자가 입력한 storage\_url의 directory-path 하위에 `#{year}/#{month}/#{day}` 폴더를 생성하여 일자를 구분<br>\* `HOUR`를 지정하면 사용자가 입력한 storage\_url의 directory-path 하위에 `#{year}/#{month}/#{day}/#{hour}`까지 폴더를 생성하여 시간별로 구분 <br> \* 그 외 사용자 정의 형식의 경우에는 #{year}, #{month}, #{day}, #{hour}에 시간이 기입됨|
| flowlog\_logger.customized_file_name | Body | String | 플로우 로그 로거가 저장소에 파일을 저장할 때, 파일 제목의 형식. <br> \* 기본값은 #{logger_id}_#{year}-#{month}-#{day}T#{hour}:#{minute}:#{second}KST |
| flowlog\_logger.admin\_state\_up | Body | Boolean | 플로우 로그 로거의 활성화 상태. `false`인 경우 비활성화되어 수집하지 않음. |
| flowlog\_logger.description | Body | String | 플로우 로그 로거의 설명 |
| flowlog\_logger.status | Body | Enum | 플로우 로그 로거의 상태 |
| flowlog\_logger.created_at | Body | Date | 플로우 로그 로거를 생성한 시간 |
| flowlog\_logger.updated_at | Body | Date | 플로우 로그 로거가 수정된 시간 |
| flowlog\_logger.error_type | Body | String | 플로우 로그 로거에 오류가 발생한 경우, 오류 이유를 표시. <br>자세한 내용은 페이지 최하단의 을 확인하시길 바랍니다.|

<details>
  <summary>예시</summary>

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

### 플로우 로그 로거 삭제하기

```
DELETE /v2.0/flowlog-loggers/{flowlogLoggerId}
X-Auth-Token: {tokenId} 
```

#### 요청

이 API는 요청 본문을 요구하지 않습니다.

| 이름 | 종류 | 형식 | 필수 | 설명 |
| --- | --- | --- | --- | --- |
| tokenId | Header | String | O | 토큰 ID |
| flowlogLoggerId | URL | UUID | O | 플로우 로그 로거 ID |

#### 응답

이 API는 응답 본문을 반환하지 않습니다.

<br>
<br>
<br>


## 플로우 로그 로깅 포트

* 플로우 로그 로깅 포트는 플로우 로그 로거가 실질적으로 캡쳐하는 포트들을 의미합니다. 플로우 로거의 resource\_type이 VPC 혹은 Subnet인 경우, 하나의 플로우 로그 로거가 여러 개의 플로우 로그 로깅 포트를 관리하게 됩니다.
* 사용자가 로거를 생성 혹은 삭제할 때, 플로우 로그는 내부적으로 해당 로거에 속해 있는 포트들을 확인하여 로깅 포트 대상으로 추가 또는 삭제를 수행합니다. 따라서 사용자가 별도로 로깅 포트를 추가/삭제 할 필요가 없습니다.
* 플로우 로그 로깅 포트는 조회 API만 제공합니다.

### 플로우 로그 로깅 포트 목록 보기

```
GET /v2.0/flowlog-logging-ports
X-Auth-Token: {tokenId} 
```

#### 요청

이 API는 요청 본문을 요구하지 않습니다.

| 이름 | 종류 | 형식 | 필수 | 설명 |
| --- | --- | --- | --- | --- |
| tokenId | Header | String | O | 토큰 ID |
| id | Query | UUID | - | 조회할 플로우 로그 로깅 포트의 ID |
| logger_id | Query | UUID | - | 조회할 플로우 로그 로깅 포트의 로거 ID |
| port_id | Query | UUID | - | 조회할 플로우 로그 로깅 포트의 포트 ID |
| network_id | Query | UUID | - | 조회할 플로우 로그의 VPC ID |

#### 응답

| 이름 | 종류 | 형식 | 설명 |
| --- | --- | --- | --- |
| flowlog\_logging\_ports | Array | Object | 플로우 로그 로깅 객체 목록 |
| flowlog\_logging\_ports.id | Body | UUID | 플로우 로그 로깅 포트 ID |
| flowlog\_logging\_ports.logger_id | Body | UUID | 해당 플로우 로그 로깅 포트가 속한 플로우 로그 로거의 ID |
| flowlog\_logging\_ports.port\_id | Body | UUID | 로깅하고 있는 포트의 ID |
| flowlog\_logging\_ports.network\_id | Body | UUID | 네트워크 ID |
| flowlog\_logging\_ports.created\_at | Body | Date | 플로우 로그 로깅 포트가 생성된 시간 |
| flowlog\_logging\_ports.updated\_at | Body | Date | 플로우 로그 로깅 포트가 수정된 시간 |

<details>
  <summary>예시</summary>

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

### 플로우 로그 로깅 포트 보기

```
GET /v2.0/flowlog-logging-ports/{flowlogLoggingPortId}
X-Auth-Token: {tokenId} 
```

#### 요청

이 API는 요청 본문을 요구하지 않습니다.

| 이름 | 종류 | 형식 | 필수 | 설명 |
| --- | --- | --- | --- | --- |
| tokenId | Header | String | O | 토큰 ID |
| flowlogLoggingPortId | URL | UUID | O | 플로우 로그 로깅 포트 ID |

#### 응답

| 이름 | 종류 | 형식 | 설명 |
| --- | --- | --- | --- |
| flowlog\_logging\_port | Body | Object | 플로우 로그 로깅 객체 |
| flowlog\_logging\_port.id | Body | UUID | 플로우 로그 로깅 포트 ID |
| flowlog\_logging\_port.logger_id | Body | UUID | 해당 플로우 로그 로깅 포트가 속한 플로우 로그 로거의 ID |
| flowlog\_logging\_port.port\_id | Body | UUID | 로깅하고 있는 포트의 ID |
| flowlog\_logging\_port.network\_id | Body | UUID | 네트워크 ID |
| flowlog\_logging\_port.created\_at | Body | Date | 플로우 로그 로깅 포트가 생성된 시간 |
| flowlog\_logging\_port.updated\_at | Body | Date | 플로우 로그 로깅 포트가 수정된 시간 |

<details>
  <summary>예시</summary>

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

## 오류 유형

플로우 로그를 사용하려는 환경이 올바르게 설정되지 않았다면 오류가 발생할 수 있습니다. 이 경우에는 flowlog_logger.error_type를 조회하여 오류 원인을 확인할 수 있습니다.


플로우 로그 로거의 상태와 오류 유형은 다음과 같습니다.

| 플로우 로그 로거 상태 | 오류 유형 | 오류 원인 | 확인 필요 사항 |
| :---: | --- | --- | --- |
| ACTIVE | - | - | - | - |
| BUILD | - | - | - | - |
| ERROR | AuthenticationSystemError | 인증 시스템에 문제가 있습니다. 고객 센터에 문의하세요. | 플로우 로그 시스템 계정이 Keystone 서버로부터 토큰 발급을 받지 못한 경우입니다. |
| ERROR | OBSConfigurationError | OBS URL 및 접근 정책을 확인하세요. | 사용자의 저장소로 더미 데이터를 보냈으나 OBS 접근 권한이 없어 403 오류가 발생한 경우입니다. 컨테이너 URL 및 접근 정책을 확인하세요. |
| ERROR | OBSServiceNotAvailableError | OBS 서비스가 동작하지 않습니다. 고객 센터에 문의하세요. | 사용자의 저장소로 더미 데이터를 보냈으나 401, 403 외의 오류가 발생한 경우입니다. |


