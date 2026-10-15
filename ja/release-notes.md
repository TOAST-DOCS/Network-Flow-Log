<!-- machine_translated: true -->

## Network > Flow Log > リリースノート

### 2026. 08. 25.

#### 機能改善/変更

* Flow Logのファイル名カスタマイズに、timezoneテンプレート変数(`#{timezone}`)をオプションとして追加できます。
    * ファイル名カスタマイズ時に`#{timezone}`変数を含めるとファイル名にtimezoneが表示され、除外すると表示されません。
    * 基本ファイル名には従来と同様にtimezoneが含まれます。timezoneの値はリージョンごとに決まります。(韓国リージョンはKST)
    * [Flow Logコンソール使用ガイド](/Network/Flow%20Log/ja/console-guide/)を参照してください。

### 2026. 05. 27.

#### 機能改善/変更

* トラフィックパス(`traffic_path`)フィールドが追加されました。
    * パケットが通過したネットワークパス(VPC Local、Internet Gateway、VPN Gateway、VPC Peering、Region Peering、Project Peering、Service Gateway)を整数値で確認できます。
    * [Flow Log概要](/Network/Flow%20Log/ja/overview/)を参照してください。

### 2025. 05. 27.

#### 新規機能追加

* Region peering gateway、Project peering gateway、Colocation gateway、ロードバランサーのネットワークインターフェイスを対象にFlow Logを作成できる機能が追加されました。

#### 機能改善/変更

* Flow LogのファイルをObject Storageに保存する際に、フォルダとファイル名を自由に編集できるよう改善されました。

### 2025. 03. 04.

#### 機能改善/変更

* Transit Hubの接続を対象にFlow Logを作成できる機能が追加されました。
* Flow Logの収集対象にVPCおよびSubnetが追加されました。

### 2024. 11. 26.

#### 機能改善/変更

* gzip圧縮機能が追加されました。
* Flow Logがサポートする統計提供情報項目の中から、ユーザーが必要な項目のみを選択して記録できるよう改善されました。サポートする統計項目については、[Flow Log概要](/Network/Flow%20Log/ja/overview/)を参照してください。

### 2024. 08. 27.

#### 新規機能追加

* Flow Logの新規サービスが追加されました。ネットワークインターフェイスで送受信されるIPトラフィック情報を収集および保存できます。
    * Flow Logは韓国(ピョンチョン)リージョンと韓国(パンギョ)リージョンでのみ利用できます。