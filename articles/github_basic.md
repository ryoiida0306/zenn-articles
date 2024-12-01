---
title: "【備忘録】GitHubの基本的な使い方"
emoji: "🐙"
type: "tech" # tech: 技術記事
topics: ["GitHub"]
published: true
---

# 新しい計算機での初期設定
## Gitのインストール(最新の情報の確認を推奨)
- [Gitの公式サイト](https://git-scm.com/)からインストーラをダウンロード
- インストーラを起動し、インストール

## SSHキーの設定(2024/11/29時点)
- 以下のコマンドを入力し、SSHキーを作成
```bash
ssh-keygen -t ed25519 -C "hogehoge@gmail.com"
```
- そのままEnterを押す
```bash
Enter file in which to save the key (/home/user01/.ssh/id_ed25519):
```
- パスフレーズを入力(空欄でも可)　※パスフレーズを入力すると、pushする際に毎回求められる
```bash
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
```
- 以下のように出力される
```bash
Your identification has been saved in /home/user01/.ssh/id_ed25519
Your public key has been saved in /home/user01/.ssh/id_ed25519.pub
The key fingerprint is:
SHA256:*************************** hogehoge@gmail.com
The key's randomart image is:
+--[ED25519 256]--+
|                 |
|                 |
|                 |
|                 |
| (sha256 image)  |
|                 |
|                 |
|                 |
|                 |
+----[SHA256]-----+
```

- `~/.ssh/id_ed25519`(秘密鍵)と、`~/.ssh/id_ed25519.pub`(公開鍵)が作成されていることを確認
- `~/.ssh/id_ed25519.pub`の中身をコピーしておく
    - 最初から最後のメールアドレスまでそのまますべてコピーする
- GitHubのトップページにアクセスし、右上のアイコンをクリック
- Settingsをクリック
- SSH and GPG keysをクリック
- New SSH keyをクリック
- Titleに任意の名前を入力
- Keyに`~/.ssh/id_ed25519.pub`の中身を貼り付け
- Add SSH keyをクリック
- 以下のコマンドでssh接続できるかを確認
```bash
ssh -T git@github.com
```
- 以下の通り出力されたらOK
```bash
Hi username! You've successfully authenticated, but GitHub does not provide shell access.
```


# 新しくプロジェクトを始める

## リポジトリの作成

- GitHubのトップページにアクセスし、新しくリポジトリを作成
- リポジトリ名を入力し、Create repositoryをクリック
    - README.mdの作成推奨
    - .gitignoreやLICENSEの設定も可能
- リポジトリのsshのURLをコピー
以下を実行
```bash
git init
git config user.name "(githubのユーザ名)"
git config user.email "(githubのメールアドレス)"
git remote add origin (リポジトリのsshのURL)
git add README.md
git commit -m "first commit"
git branch -M main
git push -u origin main
```

## 既存のプロジェクトに入るとき
- 以下を最初に実行
```bash
git clone (リポジトリのsshのURL)
git config user.name "(githubのユーザ名)"
git config user.email "(githubのメールアドレス)"
git remote add origin (リポジトリのsshのURL)
```

# 基本操作

```bash
git add (ファイル名) # ファイルをステージング
git status # 現在のステータスを確認 
git commit -m "(コミットメッセージ)" # コミット
git push origin (pushするブランチ名) # リモートリポジトリにpush
```

```bash
git branch (ブランチ名) # ブランチを作成
git checkout (ブランチ名) # ブランチを切り替え
```

```bash
git pull origin (pullするブランチ名) # リモートリポジトリからpull
# 以下と同じ
# git fetch origin (pullするブランチ名) # リモートリポジトリからfetch
# git merge origin/(pullするブランチ名) # マージ
```

## リモートの内容を反映させる時の`git fetch`と`git merge`について
- `git fetch`はリモートリポジトリの最新情報を取得するが、ローカルリポジトリにはマージされない
    - ローカルリポジトリには`origin/(リモートリポジトリのブランチ名)`という形でブランチが作成される
    - `git fetch --all`で全てのリモートリポジトリの最新情報を取得できる
- `git merge`はリモートリポジトリの最新情報をローカルリポジトリにマージする
    - コンフリクトはローカルで発生し、解消した後にpushする


## ブランチを合体するときの`git merge`について
- マージ先（マージされる方、マージ後にそのブランチが残る方）のブランチに移動してから、以下を実行
```bash
git merge (マージ元のブランチ名)
```

## mergeの詳細
- ローカルブランチ内でマージ
```bash
git merge (マージ元のブランチ名)
```

- リモートブランチに直接マージ
```bash
git merge origin/(マージ元のブランチ名)
```

## コンフリクトの解消
- コンフリクトが発生した場合、以下のようなメッセージが表示される
```bash
Auto-merging (ファイル名)
CONFLICT (content): Merge conflict in (ファイル名)
Automatic merge failed; fix conflicts and then commit the result.
```
- コンフリクトが発生したファイルを開くと、以下のような箇所が表示される
```bash
<<<<<<< HEAD
// ローカルリポジトリの変更内容
=======
// リモートリポジトリの変更内容
>>>>>>> (リモートリポジトリのブランチ名)
```

# 細かいこと

## ブランチを作成して移動
- ブランチを作成して移動
```bash
git checkout -b (ブランチ名)
```

## ブランチの削除
- ローカルリポジトリのブランチを削除
```bash
git branch -d (ブランチ名)
```
- リモートリポジトリのブランチを削除
```bash
git push origin --delete (ブランチ名)
```

## コミットの取り消し
- 直前のコミットを取り消す
```bash
git reset --soft HEAD^
```
- 直前のコミットを取り消し、変更内容も取り消す
```bash
git reset --hard HEAD^
```

## リモートリポジトリの変更を取り消す
- ローカルリポジトリの変更を取り消す
```bash
git checkout -- (ファイル名)
```
- リモートリポジトリの変更を取り消す
```bash
git fetch origin
git reset --hard origin/main
```

## ローカルリポジトリの変更を取り消す
- ローカルリポジトリの変更を取り消す
```bash
git checkout -- (ファイル名)
```

## コミットログの確認
- コミットログを確認
```bash
git log
```
- コミットログを確認（簡易版）
```bash
git log --oneline
```

## `git add .`と`git add -A`
- `git add .`はカレントディレクトリ配下
- `git add -A`は.gitディレクトリ配下

;; - `git add .`は新規ファイルと変更ファイルをステージング
;; - `git add -A`は新規ファイルと変更ファイルと削除ファイルをステージング
;; - `git add -u`は変更ファイルと削除ファイルをステージング
