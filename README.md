# 静電容量スイッチスキャン用モジュール
マルチプレクサとオペアンプを組み合わせて静電容量スイッチのマトリクスをスキャンするためのモジュールです

- **仕様は予告なく変更される場合があります**

## ピン配置

|ピン番号|ピン名|機能|
|-|-|-|
|1|S5|スキャンライン5|
|2|S7|スキャンライン7|
|3|S8|スキャンライン8|
|4|S6|スキャンライン6|
|5|~SHDN|未使用|
|6|OUT|アナログ出力|
|7|DRN|電荷引き抜きピン|
|8|GND|GND|
|9|VCC|VCC(2~5V)|
|10|SE0|ライン選択0|
|11|SE1|ライン選択1|
|12|SE2|ライン選択2|
|13|S4|スキャンライン4|
|14|S1|スキャンライン1|
|15|S2|スキャンライン2|
|16|S3|スキャンライン3|

## 回路の接続

- OUTをアナログ変換できるピンと接続
- S1~S8をスイッチマトリクスのスキャンライン(COLライン)にそれぞれ接続
- SE0~SE2を任意のデジタルピンと接続
- DRNを任意のデジタルピンと接続(外部プルアップ抵抗などを接続しないこと)

## スキャン用プログラム

- 全体の構成は[GRS-70EC](https://github.com/sekigon-gonnoc/qmk_firmware/tree/grs_70ec/keyboards/sekigon/grs_70ec)を参考にしてください

### config.hで指定するパラメータ

|名前|設定値|デフォルト|役割|
|-|-|-|-|
|MATRIX_COL_PINS|{S0,S1,S2,S3}など||スキャンラインの配列|
|HIGH_THRESHOLD|0~1024|300|key pressの判定に使う閾値|
|LOW_THRESHOLD|0~1024|200|key releaseの判定に使う閾値|
|DISCHARGE_PIN|F6など||DRNに繋いだピン|
|ANALOG_PORT|B1など||OUTに繋いだピン|
|MUX_SEL_PINS|{D1,D0,D4}など||SE0~SE2に繋いだピンの配列|

### ec_switch_matrix.[ch]

静電容量スイッチマトリクスのスキャンのため呼び出す必要がある関数は以下の2つです  

- ecsm_init
  - configを元にスキャン用のピンや変数を初期化します
- ecsm_matrix_scan
  - matrix_row_tの配列を引数として受け取って、押下状態を反映します

デバッグが必要なときは以下の関数が便利です

- ecsm_print_matrix
  - AD変換で読み取ったスイッチ状態を表示します。確認にはqmk_toolboxやhid_listenが必要です

### matrix.c

rules.mkに`CUSTOM_MATRIX=lite`を指定して上記の関数を呼び出します  
このリポジトリのmatrix.cは分割型のサンプルです

- 一体型の場合
  - `matrix_init_custom`と`matrix_scan_custom`からそれぞれ上記の関数を呼び出してください

- 分割型の場合
  - `CUSTOM_MATRIX=lite`はsplit非対応のため、split用の関数を追加する必要があります。このリポジトリのmatrix.cを参考にしてください