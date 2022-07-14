# MySQL cheating sheet

## DB の操作
### 状態遷移と状態の確認
- 既存の DB 一覧の確認
  ```sql
  show databases;
  ```
- 操作する DB の選択
  ```sql
  use <db_name>;
  ```
- 選択中の DB の確認
  ```sql
  select database();
  ```
### 作成と削除
- 新しい DB の作成
  ```sql
  CREATE DATABASE <db_name>;
  ```
- 既存 DB の削除
  ```sql
  DROP DATABASE <db_name>;
  ```
# テーブルの操作
### 状態遷移と状態の確認
- 既存のテーブル一覧の確認
  ```sql
  use <db_name>;
  show tables;
  ```
- テーブル構造の確認
  ```sql
  use <db_name>;
  desc <table_name>;
  ```
  ```sql
  use <db_name>;
  describe <table_name>;
  ```
  ```sql
  use <db_name>;
  show columns from <table_name>;
  ```
  ```sql
  use <db_name>;
  show fields from <table_name>;
  ```
- テーブルの DDL 確認
  外部キーの設定は，`desc` では確認できないので，下記で確認する．
  ```sql
  use <db_name>;
  show create table <table_name>;
  ```
  ※ `create table <table_name>` するコマンドを show するという意味で DDL が表示される．
### 作成と削除
- 新規テーブルの作成
  ```sql
  use <db_name>;

  CREATE TABLE <table_name>(
    <col_name01> <col_type> [options], 
    <col_name02> <col_type> [options], 
    ...
    );
  ```
- 既存テーブルの削除
  ```sql
  use <db_name>;
  drop table <table_name>;
  ```
### カラムの操作
- 追加  
  末尾に追加
  ```sql
  use <db_name>;
  alter table <table_name> add <col_name> <col_type>;
  ```
  指定のカラムの後に追加
  ```sql
  use <db_name>;
  alter table <table_name> add <col_name> <col_type> after <col_name>;
  ```
- 削除
  ```sql
  use <db_name>;
  alter table <table_name> drop column <col_name>;
  ```
- オプションの追加
  ```sql
  use <db_name>;
  alter table <table_name> modify column <col_name> <col_type> <options>;
  ```
- オプションの削除  
  単に `<options>` を追加しないと `<options>` が削除される
  ```sql
  use <db_name>;
  alter table <table_name> modify column <col_name> <col_type>;
  ```

### 外部キーの操作
- 外部キーの追加  
  前提条件：参照先と参照元のカラムが存在していること
  ```sql
  use <db_name>;
  alter table <table_name> add foreign key (<col_name>) references <table_name>(<col_name>) [<options>];
  ```
  - `<options>`:  
    {`on delete cascade`, `on delete restrict`, `on delete set null`},   
    {`on update cascade`, `on update restrict`, `on update set null`}
- 外部キーの削除
  ```sql
  use <db_name>;
  alter table <table_name> drop foreign key <col_key>;
  ```
  ※ 外部キーを使用すると，システムが外部キーに対応するキー名を自動で割り当てる．
  例えば，`<col_name>` に紐づく `<col_key>` は
  ```
  use <db_name>;
  show create table <table_name>; 
  ```
  で確認する．すると，`<table_name>_ibfk_1` のような id が `<col_name>` に紐づていることが確認できる．
- 削除した外部キーを元に戻す
  外部キーを削除しても，カラムに割り当てられた key 名称 (`col_key`) までは削除されない．このため，`col_key` を指定して戻すか，一度 `col_key` を削除して戻す必要がある．`col_key` を削除する場合は，他の場合で `col_key` が使用されていないことを明確にする必要がある．
  - 方法１．`col_key` を指定して外部キーを追加する場合
    ```sql
    use <db_name>;
    alter table <table_name> add constraint <col_key> foreign key(<col_name>) references <table_name>(<col_name>) [<options>];
    ```
  - 方法２．既存の `col_key` を削除して，外部キーの割り当てと一緒に作り直す場合．()
    ```sql
    use <db_name>;
    alter table <table_name> drop key <col_key>;
    alter table <table_name> add foreign key(<col_name>) references <table_name>(<col_name>) [<options>];
    ```
- 外部キーの更新  
  `on delete cascade` や `on update cascade` を後から追加したい場合がある．
  こうした場合は，一度外部キーを削除してから，再度登録する．
  ```sql
  show create <table_name>;
  ```
  の実行結果を控えておくとよい．

### レコードの操作
#### 確認
- 既存レコードの確認
  ```sql
  use <db_name>;
  select * from <table_name>;
  ```
  ```sql
  select * from <db_name>.<table_name>;
  ```
- ソートして表示する場合．(`<row_count>` の数まで表示する)
  ```sql
  select * from <table_name> order by <col_name> [ASC|DESC] limit <row_count>;
  ```
#### 追加
- 単数行レコードの追加
  ```sql
  use <db_name>;
  insert <table_name>(<col_name1>,<col_name2>,<col_name3>)values(<col_value1>,<col_value2>,<col_value3>);
  ```
  ```sql
  insert [into] <db_name>.<table_name>(<col_name1>,<col_name2>,<col_name3>)values(<col_value1>,<col_value2>,<col_value3>);
  ```
  ※ `into` の有無は動作に影響しない．
- 複数行レコードの追加
  ```sql
  use <db_name>;
  insert <table_name> values(<col_value1>,<col_value2>,<col_value3>),(<col_value1>,<col_value2>,<col_value3>);
  ```
  ```sql
  insert [into] <db_name>.<table_name> values(<col_value1>,<col_value2>,<col_value3>),(<col_value1>,<col_value2>,<col_value3>);
  ```
  ※ `into` の有無は動作に影響しない．
#### 更新
- 個々のテーブル要素に
  ```
  update <table_name> set <col_name>=<value> where <conditions>;
  ```
  - `<col_name>=<value>` (値の指定)
    - set する値を指定する
    - 複数のカラムを同時に更新する場合はカンマ `,` で繋げる
  - `<conditions>` (条件)
    - `<col_name>=<col_value>` のように指定して，指定したカラムの内，値の一致するカラムを更新する
    - 複数の条件を指定する場合は `and` で繋げる
    - その他，使用できる[演算子の一覧](https://dev.mysql.com/doc/refman/5.6/ja/non-typed-operators.html)
- 全てのカラムに同じ値を入れる
  ```
  update <table_name> set <col_name> = <value> [where true];
  ```
  ※ MySQL を `$ mysql -u <user_name> -p --safe-update` のように起動すると，where の指定が必須となる．不用意なデータの破壊を防ぐため，本番システムでは指定するとよい．逆に全てのカラムに同じ値を挿入したいのに where が求められる場合は DB に `where true` と指示する．
#### 削除
- テーブルのレコードを全て削除する
  ```sql
  use <db_name>;
  delete from <table_name>;
  ```
- 条件に一致したレコードの削除
  ```sql
  use <db_name>;
  delete from <table_name> where <conditions>;
  ```
- ソートして，指定した個数だけ削除する場合
  - 削除対象の確認
    ```sql
    select * from <table_name> where <options> order by <col_name> [ASC|DESC] limit <row_count>;
    ```
  - 削除対象の実施
    ```sql
    delete from <table_name> where <options> order by <col_name> [ASC|DESC] limit <row_count>;
    ```

## 付録

### オペレーション・サンプル
- **カラムの追加と外部キーの指定** (WIP)
  1. `my_system` DB の作成
     ```sql
     create database order_system;
     use order_system;
     ```
  2. `user` table の作成とデータの挿入
     ```sql
     create table users(id int not null auto_increment primary key, name varchar(255));
     insert users(name)value('user001'),('user002'),('user002');
     ```
  3. `orders` (命令) table の作成とデータの挿入
     ```sql
     create table orders(id int not null auto_increment primary key, order_name varchar(255));
     insert orders(order_name)value('sit'),('stand'),('walk'),('run'),('sleep');
     ```
     ※ `order` は予約語なので，カラム名には使えない．このため，`order_name` とする．
  4. `order_list` table の作成とデータの挿入
     ```sql
     create table order_list(
      id int not null auto_increment primary key, 
      order_id int not null, 
      to int,
      from int,
      foreign key(order_id) references orders(id) on delete restrict,
      foreign key(to) references users(id) on delete restrict,
      foreign key(from) references users(id) on delete restrict
      );
     insert order_list(order_id, to, from)values(3,1,2),(4,2,1),(4,3,2);
     ```
  5. データの取り出し
     ```sql
     ```
- **auto_increment を利用した挿入**
  - 手法１．column の指定で id を飛ばして指定し，残りの要素を挿入する
  - 手法２．id に 0 を指定して，auto_increment を適用可能な値であると DB に指示する

  前準備: 
  ```sql
  create database my_system;
  -- show databases;
  use my_system;
  -- select database();
  create table users(id int not null auto_increment primary key, name varchar(255));
  -- show tables;
  ```
  手法１: 
  ```sql
  insert users(name)values('example_user01'),('example_user02'); -- skip the id to use the auto_increment
  ```
  手法２: 
  ```sql
  insert users values(0,'example_user03'),(0,'example_user04'); -- 0 is treated as an 'auto_increment'-able value
  ```
  確認: 
  ```sql
  use my_system;
  select * from users;
  ```
  削除: 
  ```sql
  use my_system;
  drop table users;
  -- show tables;
  drop database my_system;
  -- show databases;
  ```

### 設計上のポイント
- [外部キーの概要と制約を使うことのメリット・デメリット](https://qiita.com/kamillle/items/5ca2db470f199c1bc3ef)

### 記号の説明
| 記号 | 説明 |
| ---- | ---- |
| []   | 括られた区間がオプションであることを示す |

### 用語
**略称**
| 略称 | 説明 |
| ---- | ---- |
| PK   | PRIMARY KEY |
| NN   | NOT NULL |
| UQ   | UNIQUE INDEX |
| BIN  | BINARY |
| UN   | UNSIGNED |
| ZF   | ZEROFILL |
| AI   | AUTO_INCREMENT |
| G    | Generated Column |

参考: [8.1.10.2 Columns Tab](https://dev.mysql.com/doc/workbench/en/wb-table-editor-columns-tab.html)

### 削除の種類
| <nobr>削除命令</nobr> | <nobr>説明</nobr> |
| -------- | ---- |
| drop     | テーブル内の，データとテーブル設定（カラムやカラムの型の設定）を削除する |
| truncate | テーブル内のレコードを全て削除する．where 句で削除範囲の指定はできない．auto_increment の採番も初期化する． |
| delete | テーブル内のレコードを削除する．where 句で削除範囲を指定できる．auto_increment の採番は初期化されない． |

### データ型とオプション
- 整数型
  | 型       | サイズ            | 説明        |
  | -------- | ----------------- | ----------- |
  | tinyint  | 8 bits            | ー          |
  | smallint | 16 bits           | ー          |
  | int      | 32 bits           | ー          |
  | bigint   | 64 bits           | ー          |
- 浮動小数点型
  | 型       | サイズ            | 説明        |
  | -------- | ----------------- | ----------- |
  | float    | 32 bits           |             |
  | double   | 64 bits           |             |
  | float(m, d) | 32 bits        | m: 合計桁数, d: m 桁の内小数点以下の桁数 |
  | double(m, d) | 64 bits       | m: 合計桁数, d: m 桁の内小数点以下の桁数 |
- 文字列型
  | 型       | 最大長            | 説明        |
  | -------- | ----------------- | ----------- |
  | char(m)  | 255               | 固定長文字列型．サイズ m の固定長領域を DB に直接確保する．サイズは常に m を示す．未使用領域はスペースで埋められる． |
  | varchar(m) | 65535 (≒64kB)  | 可変長文字列型．サイズ m の領域の内，実際に入力された文字数分の領域を DB に直接保存する．領域が不足する場合は，DB 保存先の領域が再構築される．最大サイズを m で指定する．var は variable の var |
  | tinytext   |           255          | 可変長文字列型．データへのポインタを保持する．ストレージエンジンにより，先頭の 255 byte をテーブルに格納することがある． |
  | text[(m)]  |        65,535 (≒64kB) | 可変長文字列型．データへのポインタを保持する．ストレージエンジンにより，先頭の 255 byte をテーブルに格納することがある． |
  | mediumtext |    16,777,215 (≒16MB) | 可変長文字列型．データへのポインタを保持する．ストレージエンジンにより，先頭の 255 byte をテーブルに格納することがある． |
  | longtext   | 4,294,967,295 (≒4GB)  | 可変長文字列型．データへのポインタを保持する．ストレージエンジンにより，先頭の 255 byte をテーブルに格納することがある． |
- バイナリ型
  | 型       | 最大長            | 説明        |
  | -------- | ----------------- | ----------- |
  | binary(m)    | 255            | 固定長バイナリ型．サイズ m の固定長領域を DB に直接確保する．サイズは常に m を示す．使用領域に関わらず m Bytes のストレージを消費する． |
  | varbinary(m) | 65535 (≒64kB) | 可変長バイナリ型．サイズ m の領域の内，実際に入力されたバイト数分の領域を DB に直接保存する．領域が不足する場合は，DB 保存先の領域が再構築される．最大サイズを m で指定する．var は variable の var |
  | tinyblob   |           255          | 可変長バイナリ型．データへのポインタを保持する．ストレージエンジンにより，先頭の 255 byte をテーブルに格納することがある． |
  | blob[(m)]  |        65,535 (≒64kB) | 可変長バイナリ型．データへのポインタを保持する．ストレージエンジンにより，先頭の 255 byte をテーブルに格納することがある． |
  | mediumblob |    16,777,215 (≒16MB) | 可変長バイナリ型．データへのポインタを保持する．ストレージエンジンにより，先頭の 255 byte をテーブルに格納することがある． |
  | longblob   | 4,294,967,295 (≒4GB)  | 可変長バイナリ型．データへのポインタを保持する．ストレージエンジンにより，先頭の 255 byte をテーブルに格納することがある． |
- Date 型
  | 型       | 最大長            | 説明        |
  | -------- | ----------------- | ----------- |
  | date      |                   |             |
  | datetime  |                   |             |
  | timestamp |                   |             |
  | time      |                   |             |
  | year[4]   |                   |             |
  | year[2]   |                   |             |
- キーのオプション
  | <nobr>オプション</nobr> | 説明 |
  | ---------- | ---- |
  | <nobr>primary key</nobr> | DB がテーブルのレコードを一意に特定するためにカラムに指定する key．primary key の設定されたカラムは，自動的に index が作成されるため，データを高速に検索できる．基本は 1 つのカラムを primary key に指定するが，複数のカラムの組み合わせを primary key に指定することもできる<br>**制約**<br><ul><li>重複した値が取れず，ユニークな値に制約される</ul></li><ul><li>必須項目となるため not null オプションが自動で付与されて null を設定できなくなる</ul></li> |
  | foreign key<br>(外部キー) | テーブル間で関連するカラムを接続する key．関連するデータを接合し，整合性を担保する．外部キーは親テーブルで primary key に設定されていることが多い<br>**制約**<ul><li>子テーブルには親テーブルにあるカラムしか外部キーに登録できない</li></ul>**削除の設定**<ul><li>**on delete cascade:**<br>親テーブルの行を削除すると，参照元の子テーブルの対応する行も削除する設定</ul></li><ul><li>**on delete restrict:**<br>親テーブルの削除拒否する設定．（参照を外さない限り，削除しようとするとエラーで止まる）</ul></li><ul><li>**on delete set null:**<br>親テーブルを削除すると，参照元の子テーブルの対応する行に null をセットする設定</ul></li>**アップデートの設定**<ul><li>**on update cascade:**<br>親テーブルの行を変更すると，参照元の子テーブルの対応する行も変更する設定</ul></li><ul><li>**on update restrict:**<br>親テーブルの変更を拒否する設定．（参照を外さない限り，変更しようとするとエラーで止まる）</ul></li><ul><li>**on update set null:**<br>親テーブルの行を変更すると，参照元の子テーブルの対応する行に null をセットする設定</ul></li><br>参考: [13.1.17.2 外部キー制約の使用](https://dev.mysql.com/doc/refman/5.6/ja/create-table-foreign-keys.html) |
  | unique key | テーブルのカラムが重複した値を取れないように設定する key．<br>**制約**<br><ul><li>table のレコードの内容を一意に制約する</li></ul> |
- その他のオプション
  | オプション | 説明 |
  | ---------- | ---- |
  | unsigned       | 正の数に制約する |
  | zerofill       | 数値型の未使用桁をゼロ埋めする．数値カラムに対して zerofill を指定すると，自動的に unsigned 属性が付与される |
  | not null       | カラムに必ず値を設定することを制約する |
  | auto_increment | データを追加したカラムに格納されている最大値に 1 を加算した値を自動的に設定する．最大値はデータを削除しても更新されないため，例えば，ユーザ id に auto_increment オプションを付与しておくと，ユーザを削除しても，同じ id が使用されることはなくなる．なお，truncate 命令を使用すると，auto_increment の採番は初期化される． |
  | default        | 初期値を設定する |
  
## 参考資料
- [11.1.1 数値型の概要](https://dev.mysql.com/doc/refman/5.6/ja/numeric-type-overview.html)
- [11.1.3 文字列型の概要 - mysql](https://dev.mysql.com/doc/refman/5.6/ja/string-type-overview.html)
- [11.7 データ型のストレージ要件 - mysql](https://dev.mysql.com/doc/refman/5.6/ja/storage-requirements.html)
