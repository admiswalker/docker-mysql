# MySQL application example 02

下記の表に対するクエリ例を示す．

表. ユーザの体温
| idx | user_id |      measuring_date | body_temperature |   registration_date |
| --- | ------- | ------------------- | ---------------- | ------------------- |
|   1 |     111 | '22-07-01 00:00:00' |             36.5 | '22-07-15 00:00:00' |
|   2 |     111 | '22-08-01 00:00:00' |             36.5 | '22-09-15 00:00:00' |
|   3 |     111 | '22-09-01 00:00:00' |             38.5 | '22-09-15 00:00:00' |
|   4 |     222 | '22-07-01 00:00:00' |             36.5 | '22-07-15 00:00:00' |
|   5 |     222 | '22-08-01 00:00:00' |             36.5 | '22-08-15 00:00:00' |
|   6 |     222 | '22-09-01 00:00:00' |             37.5 | '22-09-15 00:00:00' |

// '22-07-01 00:00:00', 'YY-MM-DD HH24:MI:SS'


### table の作成

1. `test_user_body_temperature` DB の作成
   ```sql
   create database test_user_body_temperature;
   use test_user_body_temperature;
   ```
2. `user_body_temperature` table の作成とデータの挿入
   ```sql
   create table user_body_temperature(idx int not null auto_increment primary key, user_id int, measuring_date date, body_temperature float, registration_date date);
   insert user_body_temperature(idx, user_id, measuring_date, body_temperature, registration_date) value
   (1, 111, '22-07-01 00:00:00', 36.5, '22-07-15 00:00:00'),
   (2, 111, '22-08-01 00:00:00', 36.5, '22-09-15 00:00:00'),
   (3, 111, '22-09-01 00:00:00', 38.5, '22-09-15 00:00:00'),
   (4, 222, '22-07-01 00:00:00', 36.5, '22-07-15 00:00:00'),
   (5, 222, '22-08-01 00:00:00', 36.5, '22-08-15 00:00:00'),
   (6, 222, '22-09-01 00:00:00', 37.5, '22-09-15 00:00:00');
   ```

### クエリ例1: 最新の日付のデータを取得する

max() -> group by -> inner join

1. max max で取得する

```sql
select user_id, max(measuring_date), max(registration_date)
from user_body_temperature
group by user_id;
```

2. 元のテーブルに inner join する

```sql
select * from user_body_temperature as lhs
inner join(
    select user_id, max(measuring_date) max_measuring_date, max(registration_date) max_registration_date
    from user_body_temperature
    group by user_id
) rhs
on lhs.user_id = rhs.user_id and lhs.measuring_date = rhs.max_measuring_date and lhs.registration_date = rhs.max_registration_date;
```

メモ：カラム名重複の解消の仕方を調べる

### クエリ例2: 1番新しいデータと2番目に新しいデータを取得する

※ row_number() は MySQL の version 8 系から使える．

```sql
select *, row_number() over (partition by user_id order by measuring_date desc) row_num from user_body_temperature;
```

- 1番新しいデータの取得
```sql
select * from (
    select *, row_number() over (partition by user_id order by measuring_date desc) row_num from user_body_temperature
) with_row_num
where row_num = 1;
```

- 2番新しいデータの取得
```sql
select * from (
    select *, row_number() over (partition by user_id order by measuring_date desc) row_num from user_body_temperature
) with_row_num
where row_num = 2;
```


















### クエリ例xxxx (ボツ)

最新のデータのみ取得する．

1. 最大値の取得
   - クエリ
     ```sql
     select max(registration_date) from user_body_temperature;
     ```
   - 実行結果
     ```console
     +------------------------+
     | max(registration_date) |
     +------------------------+
     | 2022-09-15             |
     +------------------------+
     ```

2. サブクエリで最新の登録日（max(registration_date)）を指定して，絞り込む
   - クエリ
     ```sql
     select * from user_body_temperature
     where registration_date >= (select max(registration_date) from user_body_temperature);
     ```
   - 実行結果
     ```console
     +-----+---------+----------------+------------------+-------------------+
     | idx | user_id | measuring_date | body_temperature | registration_date |
     +-----+---------+----------------+------------------+-------------------+
     |   2 |     111 | 2022-08-01     |             36.5 | 2022-09-15        |
     |   3 |     111 | 2022-09-01     |             38.5 | 2022-09-15        |
     |   6 |     222 | 2022-09-01     |             37.5 | 2022-09-15        |
     +-----+---------+----------------+------------------+-------------------+
     ```

3. サ
   - クエリ
     ```sql
     select idx, user_id, max(measuring_date), body_temperature, registration_date from user_body_temperature
     where registration_date >= (select max(registration_date) from user_body_temperature)
     group by user_id;
     ```




---

```sql
select user_id, max(registration_date)
from user_body_temperature
group by user_id;
```

---

```sql
select max(registration_date), idx, user_id, measuring_date, body_temperature
from user_body_temperature
group by idx;
```

------------


## メモ

### GROUP BY 句

GROUP BY 句は，おそらく，絞り込んだ値が一意にならない場合にエラーを返す．（絞りきれなかったので）．


## 参考

- [【SQL】グループごとに最大の値を持つレコードを取得する方法3選](https://takakisan.com/sql-max-in-each-group/)


別件メモ：
server と client を同時に動かしてるので docker compuse つかっても良さそう？
- [【Docker】docker-composeでmysqlのコンテナを立てる](https://it-blue-collar-dairy.com/mysql_on_docker-compose/)
