## Branching

### Git Flowのブランチ構造

- **main**ブランチ: 
  - このブランチは常にリリース可能な状態を保ちます
  - **正式なリリースのみ**がこのブランチにマージされます。
- **develop**ブランチ:
  - このブランチは次回リリースのための**開発統合ブランチ**です。
  - 機能開発を行う**feature**ブランチや、問題解決を行う**hotfix**ブランチがこのブランチにマージされ、最終的に**main**ブランチへマージされます
- **feature**ブランチ:
  - **新機能の開発**を行うブランチです。
  - **develop**ブランチから機能ごとにブランチを切り出し、開発が完了したら**develop**ブランチにマージします。
  - 命名例は**feature/新機能名**です。
- **release**ブランチ:
  - **次期リリースの準備**を行うブランチです。
  - **develop**ブランチから切り出し、最終的なバグフィックスや文書の更新をしてから**main**ブランチにマージします。
  - また、マージ後に**develop**にも反映させます。命名例は**release/バージョン番号**です。
- **hotfix**ブランチ:
  - **main**ブランチ上の緊急バグを修正するためのブランチです。
  - **main**ブランチから直接切り出し、修正が完了したら**main**および**develop**ブランチにマージします。
  - 命名例は**hotfix/バグ修正内容**です。

### 運用の流れ

1. 新機能を開発する際はdevelopブランチからfeatureブランチを作成し、開発が終わったらdevelopに統合します。
2. リリースの準備が整ったらdevelopからreleaseブランチを作り、最終調整をします。その後、mainに統合しタグ付けを行います。
3. releaseがmainに統合された後は、developにも同じ内容をマージしておきます。
4. 本番環境で緊急対応が必要な場合は、mainからhotfixブランチを作成し、修正をmainとdevelopに反映させます。

### ローカルでの手順

[参考: Git ブランチ作成からマージされるまでの流れ](https://qiita.com/ucan-lab/items/47967757e688e9dc42db)

0. ローカルのmainブランチ削除
   1. リモートブランチにデタッチ ``git switch -d origin/main``
   2. ローカルのmainを削除 ``git branch -D main``
1. 新しいブランチを作成
   1. リモートリポジトリの更新を取り込む ``git fetch``
   2. 派生元の**develop**ブランチから新しく(例)**feature/user-authentication**を作成する ``git switch -c feature/user-authentication origin/develop``
2. 作業した内容をコミット
   1. ``git add .``
   2. ``git commit `` commit messageのルールは**rules/commit_messages.md**を確認
3. 作業ブランチに最新のdevelopブランチを取り込む
   1. 作業途中のファイルは``git stash -u``
   2. リモートリポジトリの更新を取り込む ``git fetch``
   3. 最新の**develop**の情報を取得
      1. コンフリクトが発生したら、対象ファイルを修正し解消
      2. ``git add .``
      3. ``git rebase --continue``
      4. やり直す場合は ``git rebase --abort``でリベースする前の状態に戻す
   4. スタッシュした内容を戻す ``git stash pop``
4. 作業ブランチを整理する
   1. コミットをきれいにする ``git rebase -i origin/main``
5. リモートブランチへプッシュ
   1. ``git push origin HEAD``
6. ローカルのブランチを削除する
   1. リモートブランチの更新を取り込む ``git fetch -p`` ``-p``をつけることで削除されたリモートブランチも反映される
   2. リモートのmainブランチに切り替える ``git switch -d origin/main``
   3. ローカルブランチを削除 ``git branch -D feature/user-authentication``