### DashアプリをHeroku(+PostgreSQL)にデプロイする手順です。

0. gitとHerokuのCLIをインストールし、Herokuにアカウントを作成しておく。レポジトリをappなど適当なフォルダに入れる

1. requirement.txtを作成
  pip freezeを使う

2. Procfileを作成
  web: gunicorn app:server --log-file -<br>
　--> 意味：HTTPサーバとしてgunicornを使う。app.pyのserverを起動する。

3. アプリのフォルダに移動
  >cd /Desktop/app

4. gitをイニシャライズ
  >git init
  --> dirして.gitができてたら成功

5.herokuにログイン
  >heroku login

6.herokuにアプリを作成
  >heroku create
  --> アプリ名が自動生成され返される

7. ローカルのgitとherokuのアプリをひもづける
  >heroku git:remote -a (返ってきたアプリ名）

8. Pythonを使うためのパッケージを設定する
  >heroku buildpacks:set heroku/python

9. gitをローカルでcommit
  >git status
  >git add .
  >git commit -m "first commit"
  >git status

10. データをherokuに送る
  >git push heroku master

<ファイル変更時は>
  >git add.
  >git commit -m "added a line"
  >git push heroku master

11. HerokuのWeb画面でPostgreSQLをProvisionする<br>
該当アプリケーションのResources --> Add-onsでheroku postgres検索 --> Hobby Devを選択してProvisionをクリック

12. データベースの接続情報は、HerokuのWe画面化
  >heroku pg:credentials:url
　で確認。この情報でSQLツールから接続できる

13. Herokuのシェルに入る
  >heroku run bash
  --> ls -laとかpip freezeとかできるのでお試しあれ

14. Python起動
  bash> heroku run pyton

15. このアプリのために記述した、データベース初期化関数を走らせる
  >bash>python) from assets.database import init_db<br>
  >bash>python) from assets.database import read_data<br>
  >bash>python) init_db()<br>
  >bash>python) read_data()<br>

16. Pythonとシェルを抜けローカルに舞い戻る
  >bash>python) exit()<br>
  >bash> exit<br>

17. Herokuをリスタートする
  >heroku restart

18. アプリURLを開く。以下のコマンドでも可能
  >heroku open


データベースについて
- ローカルではsqlite, HerokuではPostgresを使う。<br>
database.pyの以下の文が制御している。<br>
engine = create_engine(os.environ.get('DATABASE_URL') or 'sqlite:///' + databese_file, convert_unicode=True , echo=True)<br>
--> 意味：エンジンとして環境変数のDATABASE_URLに指定されているものを使います。環境変数がなければ、sqlite://以下のURLにあるDBファイルを使います。<br>
         Herokuでは前者、ローカルでは後者が適用される

- models.py
SQLAlchemyで必要なファイル。テーブル定義を記述する。

- database.py
テーブルを作成しデータをロードする処理を記述している。

-　 \_\_init\_\_.py
このファイルをディレクトリに置くことで、そのディレクトリ内の pyファイルは import して別の pyファイルから利用可能となる
