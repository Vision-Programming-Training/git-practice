# git-practice

Gitの基本操作を、コードではなく簡単なテキストファイル（`memo.txt` など）を使って練習するためのリポジトリです。

上から順に進めれば、`clone` → `add` / `commit` → `diff` / `log` → `.gitignore` → `branch` / `merge` → コンフリクト解消 → `stash` → 取り消し系 → `fetch` / `pull` / `push` まで、日常的によく使うGit操作を一通り体験できます。

各手順に「実行するコマンド」と「確認すること」を書いています。エラーが出ても慌てず、`git status` で今どういう状態かを確認しながら進めてください。

---

## 0. （読み物）このリポジトリの成り立ち

このリポジトリは、作成者が最初に1回だけ `git init` して作られています。

```bash
# 作成者が最初に1回だけ行うコマンド（研修者が実行する必要はありません）
git init
git add .
git commit -m "Initial commit"
```

`git init` は「これから履歴を管理したいフォルダ」に対して最初の1回だけ行うコマンドです。すでにあるリポジトリに対して研修者が改めて実行することはないため、これは説明だけにとどめ、以降の手順では扱いません。

研修者にとっての最初の実践コマンドは、次の **1. クローン** です。

---

## 1. クローン

このリポジトリを手元に複製（クローン）します。

```bash
git clone <リポジトリの場所>
cd git-practice
```

> `<リポジトリの場所>` は配布方法に応じて読み替えてください（例: GitHubのURL、社内サーバーのパス、共有フォルダのパスなど）。

**確認すること**

```bash
git remote -v
```

`origin` という名前で、クローン元のリポジトリが自動的に登録されていることを確認します。

---

## 2. 最初のコミット

`memo.txt` を好きなテキストエディタで開き、1行追記して保存してください。

**確認すること**

```bash
git status
```

`memo.txt` が「変更されたファイル（未ステージ）」として表示されることを確認します。

**コマンド**

```bash
git add memo.txt
git status
git commit -m "memo.txtに追記"
```

`git add` の前後で `git status` の表示がどう変わるか（ステージされる、とはどういうことか）に注目してください。

---

## 3. 変更の確認（diff / log）

`memo.txt` をもう一度編集し、既存の行を書き換えたり、新しい行を追加したりしてください。保存後、コミットする前に差分を見てみます。

```bash
git diff
```

変更前後の差分が `-` / `+` で表示されることを確認します。

```bash
git add memo.txt
git commit -m "memo.txtを更新"
git log
git log --oneline
```

`git log` と `git log --oneline` の見え方の違いも比べてみてください。

---

## 4. .gitignoreの体験

コミットに含めたくないファイルを作ってみます。

```bash
echo "これは秘密のメモです" > secret.txt
git status
```

`secret.txt` が「未追跡ファイル」として表示されることを確認します。

次に、このファイルを無視対象にします。

```bash
echo "secret.txt" > .gitignore
git status
```

今度は `secret.txt` が表示されなくなり、代わりに `.gitignore` が新規ファイルとして表示されることを確認してください。

```bash
git add .gitignore
git commit -m ".gitignoreを追加"
```

---

## 5. ブランチ

新しいブランチを作って移動します。

```bash
git branch practice-branch
git switch practice-branch
# git checkout -b practice-branch でも同様のことができます
```

**確認すること**

```bash
git branch
```

`*` が付いた現在のブランチが `practice-branch` になっていることを確認します。

このブランチ上で `memo.txt` の末尾に1行追記し、コミットします。

```bash
git add memo.txt
git commit -m "practice-branchでmemo.txtに追記"
```

---

## 6. マージ（コンフリクトなし）

`main` ブランチに戻ります。

```bash
git switch main
```

`memo.txt` の**別の行**（practice-branchで編集していない箇所）を編集してコミットします。

```bash
git add memo.txt
git commit -m "mainでmemo.txtの別の行を編集"
```

`practice-branch` の変更を取り込みます。

```bash
git merge practice-branch
```

編集した箇所が別々の行であれば、自動でマージされます。`git log --oneline --graph` で履歴の分岐と合流を確認してみてください。

---

## 7. コンフリクト体験

今度はわざとコンフリクトを起こします。

```bash
git switch -c conflict-branch
```

`memo.txt` の**先頭行**を何か別の内容に書き換えて保存し、コミットします。

```bash
git add memo.txt
git commit -m "conflict-branchで先頭行を変更"
```

`main` に戻り、**同じ先頭行**を別の内容に書き換えてコミットします。

```bash
git switch main
# memo.txtの先頭行を、conflict-branchとは違う内容に編集
git add memo.txt
git commit -m "mainで先頭行を変更"
```

マージしてみます。

```bash
git merge conflict-branch
```

コンフリクトが発生し、`memo.txt` の中に次のようなマーカーが挿入されます。

```
<<<<<<< HEAD
mainでの内容
=======
conflict-branchでの内容
>>>>>>> conflict-branch
```

ファイルを開き、マーカー（`<<<<<<<` / `=======` / `>>>>>>>`）を削除しつつ、残したい内容に手で編集してください。編集が終わったら、通常のコミット手順で解消を確定します。

```bash
git add memo.txt
git commit -m "コンフリクトを解消"
```

---

## 8. stash

作業途中の変更を一時的に退避する練習です。

`memo.txt` を編集します（コミットはしません）。

```bash
git status
git stash
git status
```

`git stash` の後、`memo.txt` の変更が消えて（退避されて）、作業ディレクトリがきれいな状態に戻ることを確認します。

```bash
git stash list
git stash pop
git status
```

`git stash pop` で退避していた変更が戻ってくることを確認してください。

---

## 9. 取り消し系（restore / reset / revert）

それぞれ「取り消せる範囲」が違います。1つずつ試してください。

**restore: ステージする前の変更を取り消す**

```bash
# memo.txtを適当に編集（addはまだしない）
git restore memo.txt
git status
```

編集内容が消え、直前のコミット時点の内容に戻ることを確認します。

**reset: ステージを解除する**

```bash
# memo.txtを編集してadd
git add memo.txt
git reset memo.txt
git status
```

ファイルの変更内容自体は残ったまま、ステージだけが解除されることを確認します。

**revert: すでにコミットした内容を打ち消す**

```bash
git commit -am "取り消し練習用のコミット"
git revert HEAD
git log --oneline
```

`revert` は過去のコミットを削除するのではなく、「打ち消すための新しいコミット」を追加する点が `reset` と異なります。履歴が消えずに残ることを `git log` で確認してください。

---

## 10. fetch / pull / push

実際の `origin`（クローン元のリポジトリ）には書き込み権限がないことも多いため、自分のクローンの中に「もう一人の自分」役の2つ目の作業コピーを用意して、push/fetch/pullの一連の流れを体験します。

**練習用リモートの準備**

`git-practice` フォルダの外側で作業します。

```bash
cd ..
git clone --bare git-practice practice-remote.git
```

これが「共有リモート」役になります。`git-practice` に、このリモートを2つ目のリモートとして登録します。

```bash
cd git-practice
git remote add practice-remote ../practice-remote.git
git remote -v
```

`origin` と `practice-remote` の2つが登録されていることを確認してください。

もう1つ、「同僚」役の作業コピーを作ります。

```bash
cd ..
git clone practice-remote.git colleague-copy
cd colleague-copy
git remote -v
```

こちらは `practice-remote.git` からのクローンなので、`origin` が `practice-remote.git` を指していることを確認してください。

**push**

`git-practice` 側（自分）で `memo.txt` を編集し、コミットします。

```bash
cd ../git-practice
# memo.txtを編集
git add memo.txt
git commit -m "push練習用の変更"
git push practice-remote main
```

**fetch**

`colleague-copy` 側（同僚）に切り替えます。

```bash
cd ../colleague-copy
git fetch origin
```

まだ自分の `main` ブランチには変更が反映されていないことを確認します。

```bash
git log --oneline main
git log --oneline origin/main
```

`origin/main` の方だけ、新しいコミットが増えていることを確認してください（`fetch` は取ってくるだけで、自分のブランチには取り込まない、という点がポイントです）。

**pull**

```bash
git pull origin main
git log --oneline main
```

`pull` を実行すると `fetch` + 取り込み（マージ）がまとめて行われ、自分の `main` にも変更が反映されることを確認してください。

---

## 11. ログの見方

最後に、ここまでの一連の履歴を俯瞰してみます。

```bash
git log --oneline --graph --all
```

ブランチの分岐、マージ、コンフリクト解消のコミットなどが、どのような形で履歴に残っているかを確認して終了です。
