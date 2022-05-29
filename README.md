# docker-mysql


## コンテナの取り扱い
- 起動
  ```
  cd docker
  ./docker_run_mysql.sh
  ```
  ※ ログ情報など，起動中のメッセージを取得したい場合は，shell の -d オプションを外すこと．
- 起動中コンテナの確認
  ```
  docker ps
  ```
- コンテナの削除
  ```
  docker rm -f <CONTAINER ID>
  ```

## アプリケーションからの接続
起動したコンテナは様々なアプリケーションから接続できる．
例えば，GUI アプリなら MySQL Workbench，
コマンドラインなら mysql-client，
プログラムで Python のライブラリから接続するなら `mysql.connector` がある．

アプリケーションでの接続は `docker/docker_run_with_console.sh` に記載の通り，
下記の設定で接続できる．

### ログインユーザ
- root user で接続する場合
  ```
  user name: root
  pass: rootpass
  port: 3306
  ```
- test user で接続する場合
  ```
  user name: testuser
  pass: testpass
  port: 3306
  ```

## 付録
### 作成工程
#### docker image と起動オプションの調整
- [Dockerでいつでも作り直せるローカルDB(mysql)作ってみた]()
- [docker run するときにUID,GIDを指定する](https://qiita.com/manabuishiirb/items/83d675afbf6b4eea90e4)


