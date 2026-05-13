## Network > Flow Log > 概要
Flow Logサービスは、ユーザーのネットワークインターフェースで送受信されるパケットを分析して統計を提供します。このサービスを使用すると、ネットワークインターフェースに設定された**Security Groups**ルールによって許可または拒否されたパケットの数、サイズなど、様々な統計を確認できます。Flow Logサービスを活用すると、ユーザーのネットワークインターフェースが正しくトラフィックを送受信したか、誰と通信を行ったか、そして外部からどのような侵入の試みがあったかなどを確認できます。



### 主な機能

* Flow Logサービスは、ネットワークインターフェースで送受信される全てのパケットのヘッダを検査します。現在は、インスタンスのネットワークインターフェースとTransit Hub接続にのみ機能を提供します。

* ただし、L2タイプはEthernet、L3タイプはIPv4、L4タイプはTCP/UDP/ICMPの場合にのみ、ヘッダを検査して統計を提供します。検査されたパケットは、5-tupleを基準に集計されます。

* 現在、Flow Logサービスは**Object Storage**を保存領域として活用しています。ユーザーが設定した収集間隔ごとに**Object Storage**にファイルが作成され、このファイルをダウンロードして実際の統計を確認できます。

* 統計を確認することで、**Security Groups**が正しく設定されているかどうか、外部からの侵入の試みなどを確認できます。


### サービス対象

* インスタンスのポート経由で送受信されるパケットの接続情報、統計などを収集/確認したい場合

* 使用中のネットワークサービスを流れるパケットの接続情報、統計などを収集/確認したい場合

* **Security Groups**設定によって許可または遮断されたパケットの接続情報、統計を収集/確認したい場合

* インスタンスへのパケット流入履歴を確認し、疑わしいアドレスを遮断してインスタンスのセキュリティ強化を図りたい場合


### 用語

Flow Logサービスで使用するリソースと用語を説明します。

* flowlog logger: ユーザーが作成したFlow Logロガーです。収集間隔、フィルタなどを設定できます。
* flowlog logging port: ユーザーが作成したFlow Logロガーによって、実質的に収集が行われるネットワークインターフェースです。
* 5-tuple: 一般的なL4パケットヘッダで次のもので構成されるタプルを意味します(プロトコル、送信元アドレス、宛先アドレス、送信元ポート番号、宛先ポート番号)。5-tupleが同じ場合、同じフローとみなされます。L4がないICMPの場合は、送信元ポート番号と宛先ポート番号を0とみなします。



## 統計の提供情報項目
Flow Logサービスがパケットを収集及び集計し、ユーザーに提供する項目は次のとおりです。


| 番号 | フィールド | 説明 | 単位 | 備考 |
| --- | --- | --- | --- | --- |
| 1| timestamp_start | 該当の5-tupleが初めて確認された時間 | UNIX TIMESTAMP |  |
| 2| timestamp_end | 該当の5-tupleが最後に確認された時間 | UNIX TIMESTAMP | |
| 3| interface_id | ネットワークインターフェースID | UUID |  |
| 4| owner_type | ネットワークインターフェースを所有する機器の種類 | `instance`、`transithub_attachment`、`inter_project_peering`、`inter_region_peering`、`colocation_gateway` または `loadbalancer` | |
| 5| owner_id | ネットワークインターフェースを所有する機器のID | UUID | |
| 6| subnet_id | ネットワークインターフェースを所有するサブネットのID | UUID | |
| 7| vpc_id | ネットワークインターフェースを所有するVPCのID | UUID | |
| 8| region | リージョン情報 | `KR1`、`KR2`、`KR3` | * KR1: 韓国(パンギョ)リージョン <br> * KR2: 韓国(ピョンチョン)リージョン <br> * KR3: 韓国(光州)リージョン |
| 9| protocol | 5-tupleにおけるプロトコル番号 | IANAによって割り当てられたプロトコル番号を表します。<br> * 各番号に応じたプロトコルは次のとおりです。1: ICMP、6: TCP、17: UDP <br> * これら以外は収集しません。|
| 10 | src_addr | 送信元アドレス | IPv4アドレス | |
| 11 | dst_addr | 宛先アドレス | IPv4アドレス | |
| 12 | src_port | 送信元ポート番号 | Integer | ICMPは0とみなします。 |
| 13 | dst_port | 宛先ポート番号 | Integer | ICMPは0とみなします。 |
| 14 | tcp_flag | TCP flag | Integer | TCP flagは、収集間隔内にキャプチャされたパケットを`bitwise OR`処理して表記します。<br>詳細については、表の下部のTCP flagsをご参照ください。 |
| 15 | packets | 収集間隔中に確認されたパケット数 | Integer | |
| 16 | bytes | 収集間隔中に確認されたパケットサイズの合計 | Byte | |
| 17 | direction | 収集された5-tupleのパケットのフロー方向 | `ingress`、`egress` または `unknown` | |
| 18 | filter | 収集された5-tupleのSecurity Groups判定結果 | `ACCEPT` または `DROP` |
| 19 | transithub_drop_no_route_packets | ルーティング経路がなく、Transit Hubルーターがドロップしたパケット数 | Integer | Transit Hubに関連する項目として、Transit Hub以外のインターフェースは-1と表記されます。 |
| 20 | transithub_drop_no_route_bytes | ルーティング経路がなく、Transit Hubルーターがドロップしたパケットサイズの合計 | Byte | Transit Hubに関連する項目として、Transit Hub以外のインターフェースは-1と表記されます。 |
| 21 | transithub_drop_black_hole_packets | Transit Hubルーターでブラックホールルーティングと決定されてドロップされたパケット数 | Integer | Transit Hubに関連する項目として、Transit Hub以外のインターフェースは-1と表記されます。 |
| 22 | transithub_drop_black_hole_bytes | Transit Hubルーターでブラックホールルーティングと決定されてドロップされたパケットサイズの合計 | Byte | Transit Hubに関連する項目として、Transit Hub以外のインターフェースは-1と表記されます。 |
| 23 | status | ログ状態 | `OK` または `SKIPDATA` または `NODATA` | * OK: 正常にロギングされた5-tupleです。<br> * SKIPDATA: Flow Logで提供する内部容量を超過し、該当の収集間隔期間中に収集されなかったパケットが存在します。<br> * NODATA: 該当の収集間隔内に収集されたデータがありません。 |
| 24 | traffic_path | 収集された5-tupleのトラフィック経路 | Integer | パケットが流れたネットワーク経路を1～7の整数値で表記します。<br> * 1: VPC Local(同一VPC内のリソース間通信) <br> * 2: Internet Gateway(インターネットへ送信されるトラフィック、フローティングIPを含む) <br> * 3: VPN Gateway(Site-to-Site VPN経由のオンプレミス接続) <br> * 4: VPC Peering(同じプロジェクト内のVPCピアリング) <br> * 5: Region Peering(異なるリージョン間のVPCピアリング) <br> * 6: Project Peering(異なるプロジェクト、同じリージョンのVPCピアリング) <br> * 7: Service Gateway(NHN Cloud内部サービスへのアクセス、例: Object Storage) |


### TCP Flag
* TCP接続が短い場合、TCP Active openを試みる側からSYN、FINを収集間隔内に送信することがあります。この場合、SYN \| FIN (2 | 1 = 3)が記録されます。


* 反対に、受信データとしては収集間隔内にSYN \| ACK、そしてFINが受信されることがあります。この場合、SYN \| ACK \| FIN (16 | 2 | 1 = 19)が記録されます。

* SYN、ACK、RST、FINの各数字は、TCP header tcp flag bit field(RFC 793, section 3.1. Header Format)に従います。

    * FIN: 1
    * SYN: 2
    * RST: 4
    * ACK: 16

* PSH flagのみ存在するパケット、ACK flagのみ存在するパケット、及び一般的にトラフィックを送信する際に使用するPSH \| ACK flagは収集に含めません。つまり、SYN、SYN \| ACK、FIN \| ACK、RST、FINのみ記録します。
* URG(urgent)、ECE(ECN-echo)、CWR(congestion window reduced)は提供しません。

## 注意事項

### 収集間隔
* 収集間隔を長く設定した場合、実際には異なる接続であっても同じ5-tupleとして収集される可能性があります。

    * 収集間隔内に同一の5-tupleで複数回の接続確立/終了を繰り返すと、これらの接続が論理的にそれぞれ異なる接続であったとしても、同じ5-tupleとして集計されます。

    * したがって、状況に応じて適切な収集間隔を設定することを推奨します。

### Flow Logがキャプチャしないトラフィック

* IPv6トラフィックは記録しません。
* インスタンスへ送受信されるマルチキャストトラフィックは記録しません。
* インスタンスの状態を把握するために169.254.169.0/24と通信するトラフィックは記録しません。
* トラフィックミラーリングは記録しません。
* ARPパケットは記録しません。
* インスタンスを含む物理機器、またはネットワークサービスの物理機器において、一時的なネットワークの輻輳によって発生するDROPは収集対象ではありません。

### Transit Hub接続にFlow Logを指定して使用する際の注意事項

* Transit Hubのマルチキャストトラフィックは、Transit Hubを基準としてTransit Hub経由で流入(ingress)するパケットのみを記録します。1つまたは複数の接続を通じて送信されるマルチキャストトラフィックは記録しません。
* Transit Hubを流れるパケットは、Transit Hubルーターのドロップの有無に関係なく、全てACCEPTに1回ずつ記録されます。Transit Hubルーターで実際にドロップされたパケットは、別の行にDROPと共に記録されます。
* Transit Hubは**接続確立パケットのみ収集(connection setup only)**オプションの影響を受けず、接続状態に関係なく全てのパケットを収集します。

### ロードバランサーにFlow Logを指定して使用する際の注意事項

* 現在、ロードバランサーはACCEPTパケットのみを収集します。ロードバランサーに設定されたIP ACLによってDROPされたパケットの収集は、今後サポートする予定です。

* ロードバランサーへのアクセスを試みるパケット、ロードバランサーとメンバー間の一般パケットだけでなく、ヘルスチェックパケットも一緒に収集します。
* 該当のサービスに接続されたFlow Logは**接続確立パケットのみ収集(connection setup only)**オプションの影響を受けず、接続状態に関係なく全てのパケットを収集します。

### ピアリングゲートウェイ及びコロケーションゲートウェイにフローログを指定して使用する際の注意事項

* VPCピアリングゲートウェイは現在サポートしていません。
* ユーザーが明示的にDROPを設定できるサービスではないため、DROPはサポートしていません。
* 該当のサービスに接続されたFlow Logは**接続確立パケットのみ収集(connection setup only)**オプションの影響を受けず、接続状態に関係なく全てのパケットを収集します。
