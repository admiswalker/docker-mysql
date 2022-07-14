# MySQL application examples


## select 例

下記の表について，select 例を示す．

表. user_properties table
| management_id | name    | property  |
|---------------|---------|-----------|
|             1 | user001 | phone     |
|             2 | user001 | glasses   |
|             3 | user001 | watch     |
|             4 | user002 | phone     |
|             5 | user002 | book      |
|             6 | user002 | watch     |
|             7 | user003 | phone     |
|             8 | user003 | glasses   |
|             9 | user003 | chocolate |

### table の作成
1. `test_subquery` DB の作成
   ```sql
   create database test_subquery;
   use test_subquery;
   ```
2. `user` table の作成とデータの挿入
   ```sql
   create table user_properties(management_id int not null auto_increment primary key, name varchar(255), property varchar(255));
   insert user_properties(name,property)value
   ('user001','phone'),('user001','glasses'),('user001','watch'),
   ('user002','phone'),('user002','book'),('user002','watch'),
   ('user003','phone'),('user003','glasses'),('user003','chocolate');
   ```

### select の例

- phone を持っている user の management_id と name, property を取り出す．
  ```sql
  select management_id,name,property from user_properties where property='phone';
  ```
  または
  ```sql
  select management_id,name,property from user_properties where property='phone';
  ```
- phone を持っている user の management_id と name を取り出す．
  ```sql
  select management_id,name from user_properties where property='phone';
  ```

### サブクエリ

- glasses を持っている user の id を調べて，一致する id の結果を返す
  ```sql
  select management_id,name,property from user_properties where name in (select name from user_properties where property='phone');
  ```
  ※ where の条件式では in を使う．in 句では複数値を取れる．なお，`=` は値を 1 つしか取れないので，`Subquery returns more than 1 row` というエラーを返す．

### 参考
- [SQLのサブクエリ（副問合せ）とは？～初心者向けに書き方を解説～](https://products.sint.co.jp/siob/blog/what-is-sql-subquery)




## 付録


## 参考資料
