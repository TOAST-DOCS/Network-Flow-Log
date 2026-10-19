<!-- pre-align:aligned sig=5a7fc6be2bc3 -->

## Network > Flow Log > 릴리스 노트

### 2026. 08. 25.

#### 기능 개선/변경

* Flow Log 파일명 커스터마이징에 timezone 템플릿 변수(`#{timezone}`)를 옵션으로 추가할 수 있습니다.
    * 파일명 커스터마이징 시 `#{timezone}` 변수를 포함하면 파일명에 timezone이 표시되고, 제외하면 표시되지 않습니다.
    * 기본 파일명에는 기존과 동일하게 timezone이 포함됩니다. timezone 값은 리전별로 결정됩니다. (한국 리전은 KST)
    * [Flow Log 콘솔 사용 가이드](/Network/Flow%20Log/ko/console-guide/)를 참고하세요.

### 2026. 05. 27.

#### 기능 개선/변경

* 트래픽 경로(`traffic_path`) 필드가 추가되었습니다.
    * 패킷이 통과한 네트워크 경로(VPC Local, Internet Gateway, VPN Gateway, VPC Peering, Region Peering, Project Peering, Service Gateway)를 정수 값으로 확인할 수 있습니다.
    * [Flow Log 개요](/Network/Flow%20Log/ko/overview/)를 참고하세요.

### 2025. 05. 27.

#### 신규 기능 추가

* Region peering gateway, Project peering gateway, Colocation gateway, 로드 밸런서의 네트워크 인터페이스를 대상으로 Flow Log를 생성할 수 있도록 기능이 추가되었습니다.

#### 기능 개선/변경

* Flow Log의 파일을 Object Storage에 저장할 때, 폴더와 파일 이름을 자유롭게 편집할 수 있도록 개선되었습니다.

### 2025. 03. 04.

#### 기능 개선/변경

* Transit Hub의 연결을 대상으로 Flow Log를 생성할 수 있도록 기능이 추가되었습니다.
* Flow Log 수집 대상에 VPC 및 Subnet이 추가되었습니다.

### 2024. 11. 26.

#### 기능 개선/변경

* gzip 압축 기능이 추가되었습니다.
* Flow Log가 지원하는 통계 제공 정보 항목 중에서 사용자가 원하는 항목만 선택하여 기록을 남길 수 있도록 개선되었습니다. 지원하는 통계 항목은 [Flow Log 개요](/Network/Flow%20Log/ko/overview/)를 참고하세요.

### 2024. 08. 27.

#### 신규 기능 추가

* Flow Log 신규 서비스가 추가되었습니다. 네트워크 인터페이스에서 송/수신되는 IP 트래픽 정보를 수집 및 저장할 수 있습니다.
    * Flow Log는 한국(평촌) 리전과 한국(판교) 리전에서만 이용할 수 있습니다.
