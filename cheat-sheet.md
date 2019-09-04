# よく使うコマンド
​
## Mac ショートカット
​
### キー
- `⌘`: Command
- `⇧`: Shift
- `^`: Control
- `⌥`: Option
​
### ショートカット
- 隠しファイル表示: `⌘ + ⇧ + .`
- ロック: `Fn + ⇧ + ^ + ESC`
​
## git
​
### もろもろ
```sh
# 未ステージングの更新を破棄する
git checkout .
​
# marge時に必ずマージコミットを作る
git merge feature/branch --no-ff
# コミットまで完了したマージを取り消す（push前に限る！！）
git reset --hard ORIG_HEAD
​
​
# 新しいブランチをリモートに反映させる
git push origin feature/new_branch
# 新しいリモートブランチをローカルに持ってくる
git branch feature/new_branch origin/feature/new_branch
​
​
# merge済みブランチの一覧
git branch --merged
# ローカルブランチの名前を変更する
git branch -m current_name new_name
# ローカルブランチの削除
git branch -d feature/local_branch
# ローカルブランチをマージしたかにかかわらず削除
git branch -D feature/local_branch
# リモートブランチの削除
git push --delete origin feature/branch
# リモートブランチをきれいにする
git remote prune origin
​
​
# タグを打つ
git tag develop/tag_name
# タグをリモートに反映させる
git push origin develop/tag_name
# ローカルのタグを削除する
git tag -d develop/tag_name
# リモートのタグを削除する
git push origin :develop/tag_name
​
​
# 直前のコミットメッセージを修正する
git commit --amend -m"修正内容"


# submoduleを追加する(REPO_DIRを指定するとそのディレクトリにソースが取得される)
git submodule add {REPO} {REPO_DIR}
# ネストしたsubmoduleをすべて取り込む
git submodule update --init --recursive
```
​
### rebase
```sh
# 派生元ブランチ(BASE_BRANCH)の更新に合わせてコミットを変更する(FEATURE_BRANCHにて)
git rebase <BASE_BRANCH>
```
​
```sh
# 現在のブランチのHEAD含む<NUM_FROM_HEAD>個分のコミットを対話的に編集する
git rebase -i HEAD~<NUM_FROM_HEAD>
# コミットが上から古い順に並んだエディタが開くので行頭を以下の単語に置き換えて:wq（他は検索して）。
# p, pick = そのコミットを使用する
# s, squash = そのコミットを直前のpickを指定したコミットに統合する。メッセージも統合（多分vimが開く）。
# f, fixup = squashと同じように直前のpickを指定したコミットに統合する。メッセージは破棄。
```
​
```sh
# rebase に失敗した場合に切り戻す
git rebase --abort
```
​
## AWS CLI
​
### KMS
```sh
# dragongate-cloudpack_deploy
# KMSによる暗号化
aws kms encrypt --key-id alias/dragongate-envvars --plaintext "<KEY/SECRET>" --profile '<YOUR_PROFILE>'
# 復号化（jqコマンドをHomebrewなどでインストールしておく）
aws kms decrypt --profile '<YOUR_PROFILE>' --ciphertext-blob fileb://<(echo '<暗号化済みの文字列>' | base64 -D) | jq .Plaintext --raw-output | base64 -D
```
​
### S3
```sh
# 特定のファイルをS3へコピーする
aws s3 cp <SOURCE_FILE> s3://<BUCKET_NAME>/<PATH>/<TO>/<DEST>/<OBJECT> --profile <YOUR_PROFILE>
​
# S3のバケット内の特定のパス以下の特定の文字列を検索する
aws s3 ls s3://<BUCKET_NAME>/<PATH>/<TO>/<DIR>/ --profile <YOUR_PROFILE> --recursive | grep <WORD_TO_SEARCH>
​
# S3の特定のフォルダ以下をまるごとダウンロードする
aws s3 cp s3://<BUCKET_NAME>/<PATH>/<TO>/<DIR> <PATH>/<TO>/<LOCAL_DIR> --recursive --profile <YOUR_PROFILE>
```
​
### SQS からメッセージを受信して Queue から削除する
1. まず現在 Queue に溜まっているメッセージの数を確認する
    ```sh
    aws sqs get-queue-attributes --queue-url <SQSのページで確認できるURL> --attribute-names ApproximateNumberOfMessages
    ```
1. Queue からメッセージを取り出す
    ```sh
    aws sqs receive-message --queue-url <SQSのページで確認できるURL>
    ```
1. Queue からメッセージを削除する
    ```sh
    aws sqs delete-message --queue-url <SQSのページで確認できるURL> --receipt-handle <recieve-messageで返ってきた値>
    ```
​
## node
​
### nodenv
```sh
nodenv install --list   # 利用可能なnode一覧
nodenv install 10.16.0  # 現時点でのLATEST
nodenv rehash           # node時/npm packageの-gインストール時に必要
nodenv global 10.16.0   # システム全体に適用
nodenv local 10.1.0     # カレントディレクトリのみに適用
​
# 設定中のnodeを解除する
nodenv local --unset    # globalが使われるようになる
nodenv global --unset   # systemがあればそれになる
```
​
### package manager
```sh
# インストール
npm install [--save/-S --save-dev/-D] {package}
yarn add [--dev] {package}
​
# アンインストール
npm uninstall [--save/-S --save-dev/-D] {package}
yarn remove {package}
​
# アップデート
npm update [-g] {package}       # packageを指定しない場合は全部
yarn [global] upgrade {package}
​
# パスを通さず実行
npx {package}
yarn run -s {package} 
```
​
## python
​
### pyenv
```sh
pyenv install --list
pyenv install X.X.XX
pyenv versions
pyenv global 3.6.5
pyenv local 2.7.10
```
​
### venv
```sh
cd [project dir]
python -m venv [newenvname] # 基本的に .venv で作るルールで
source [newenvname]/bin/activate
deactivate
```
​
## clasp
```sh
# インストール
yarn add -D @google/clasp
yarn add @types/google-apps-script
​
# ログイン
clasp login # https://script.google.com/home/usersettings でAPIをオンに
​
# 新規作成
clasp create {project_name} --rootDir ./src
clasp pull
​
# 既存スクリプトを取得
clasp clone {script_id} --rootDir ./src
​
# デプロイ
clasp push
```
​
## アーカイブ関連
```sh
tar cfvz DEST_FILE.tar.gz SOURCE_FILE.csv   # tar.gzへの圧縮
gzip -d TARGET_FILE.tar.gz                  # tar.gzファイル --> tarファイルの展開
​
brew install unar # https://theunarchiver.com/
lsar path/to/archive.zip        # アーカイブの中身を表示
unar path/to/archive.zip        # アーカイブを展開
unar -o ./out_dir/ archive.7z   # 展開先ディレクトリを指定（ない場合は作成）
```
​
## リモート作業
```sh
# SCPでのアップロード
scp -i <YOUR_PEM_FILE> <SOURCE_FILE> USER@IP_OR_HOST:~/PATH/TO/REMOTE/DEST/DIR/
# SCPでのダウンロード
scp -i <YOUR_PEM_FILE> USER@IP_OR_HOST:~/PATH/TO/REMOTE/SOURCE.tar.gz PATH/TO/LOCAL/DEST/DIR/
​
# SSH
ssh -i <YOUR_PEM_FILE> USER@IP_OR_HOST
```