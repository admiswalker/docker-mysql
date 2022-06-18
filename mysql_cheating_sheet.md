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
## Table の操作
### 状態遷移と状態の確認
- 既存の table 一覧の確認
  ```sql
  show tables;
  ```
- 既存 table の構造確認
  ```sql
  SHOW COLUMNS FROM <table_name>;
  ```
  ```sql
  DESCRIBE <table_name>;
  ```
- table 構造の確認
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
### 作成と削除
- 新規 table の作成
  ```sql
  use <db_name>;

  CREATE TABLE <table_name>(
    <col_name01> <col_type> [options], 
    <col_name02> <col_type> [options], 
    ...
    );
  ```
  - データ型
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
      | varchar(m) | 65535 (≒64kB)  | 可変長文字列型．サイズ m の領域の内，実際に入力された文字数分の領域を DB に直接保存する．領域が不足する場合は，DB 保存先の領域が再構築される．最大サイズを m で指定する． |
      | tinytext (tinyblob)     |           255          |             |
      | text (blob)             |        65,535 (≒64kB) |             |
      | mediumtext (mediumblob) |    16,777,215 (≒16MB) |             |
      | longtext (longblob)     | 4,294,967,295 (≒4GB)  |             |

      ※ blob 型は binary データを扱うデータ型．データのサイズは指定しない．  
    ref: [11.1.3 文字列型の概要 - mysql](https://dev.mysql.com/doc/refman/5.6/ja/string-type-overview.html)  
    ref: [11.7 データ型のストレージ要件 - mysql](https://dev.mysql.com/doc/refman/5.6/ja/storage-requirements.html)
  ```
  verchar(string_length)
  ```
  - options
    ```
    not null
    auto_increment
    primary key
    ```
- 既存 table の削除
  ```sql
  use <db_name>;
  drop table <table_name>;
  ```


```
```


















