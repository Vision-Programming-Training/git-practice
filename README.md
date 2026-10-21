# git-practice

Gitの基本操作を、コードではなく簡単なテキストファイル（`memo.txt` など）を使って練習するためのリポジトリです。

上から順に進めれば、
1. `clone` → 自分専用ブランチの作成 → `add`
2. `commit` → `diff`
3. `log` → `.gitignore` → `branch`
4. `merge` → コンフリクト解消 → `stash` → 取り消し系 → `fetch`
5. `pull` / `push`

まで、日常的によく使うGit操作を一通り体験できます。

> このリポジトリの `origin`（クローン元）はみんなで共有するものです。<br>
> 共有の `main` ブランチを直接汚さないよう、研修を始めたらまず自分専用のブランチを作り、<br>
> 以降の作業はすべてそのブランチ上で行ってください。<br>
> （手順は **2. 自分専用ブランチを作る** を参照）

各手順に「実行するコマンド」と「確認すること」を書いています。<br>
エラーが出ても慌てず、`git status` で今どういう状態かを確認しながら進めてください。

## はじめに
各セクションのコマンドを実行する際は、`git bash` を利用してください。<br>
コマンドプロンプトやPowerShellだと対応していないコマンドがあるかもしれません。

---

## 0. （実施済み）git初期化

**目的: `git init` は最初の1回だけ行うものだと知る**

このリポジトリは、作成者が最初に1回だけ `git init` して作られています。

```bash
# 作成者が最初に1回だけ行うコマンド（研修者が実行する必要はありません）
git init
git add .
git commit -m "Initial commit"
```

`git init` は「これから履歴を管理したいフォルダ」に対して最初の1回だけ行うコマンドです。<br>
本リポジトリにおいては既に実行済みです。

研修者にとっての最初の実践コマンドは、次の **1. クローン** です。

---

## 1. クローン

**目的: リモートのリポジトリを手元に複製する**

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

## 2. 自分専用ブランチを作る

**目的: 共有リモートを汚さない、自分だけの作業スペースを用意する**

`origin` はほかの研修者も同じものを見に行く共有リポジトリです。<br>
共有の `main` ブランチに直接コミットすると、他の人の作業とぶつかってしまいます。<br>
そこで、これ以降の作業はすべて自分専用のブランチ上で行います。

```bash
git switch -c mywork
```

> `mywork` は例です。<br>
> 実際には自分の名前やIDを使った、他の人とかぶらないブランチ名（例: `taro-practice`）にしてください。
> 以降の手順では `mywork` と書いている部分を、自分が付けたブランチ名に読み替えてください。

**確認すること**

```bash
git branch
```

`*` が付いた現在のブランチが `mywork`（自分が付けた名前）になっていることを確認します。<br>
以降の **3〜11** の練習は、すべてこのブランチの上で行います。

---

## 3. 最初のコミット

**目的: `add` → `commit` の基本サイクルを体験する**

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

## 4. 変更の確認（diff / log）

**目的: 変更内容と履歴を確認する方法を身につける**

`memo.txt` をもう一度編集し、既存の行を書き換えたり、新しい行を追加したりしてください。<br>
保存後、コミットする前に差分を見てみます。

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

## 5. .gitignoreの使い方

**目的: 特定のファイルを追跡対象から除外する方法を理解する**

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

## 6. ブランチ

**目的: ブランチを作成して切り替える基本操作を覚える**

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

## 7. マージ（コンフリクトなし）

**目的: ブランチの変更を取り込む基本的な流れを体験する**

自分の作業ブランチ（`mywork`）に戻ります。

```bash
git switch mywork
```

`memo.txt` の**別の行**（practice-branchで編集していない箇所）を編集してコミットします。

```bash
git add memo.txt
git commit -m "myworkでmemo.txtの別の行を編集"
```

`practice-branch` の変更を取り込みます。

```bash
git merge practice-branch
```

編集した箇所が別々の行であれば、自動でマージされます。<br>
`git log --oneline --graph` で履歴の分岐と合流を確認してみてください。

---

## 8. コンフリクト体験

**目的: マージコンフリクトの発生原因と手動での解消方法を体験する**

今度はわざとコンフリクトを起こします。

```bash
git switch -c conflict-branch
```

`memo.txt` の**先頭行**を何か別の内容に書き換えて保存し、コミットします。

```bash
git add memo.txt
git commit -m "conflict-branchで先頭行を変更"
```

自分の作業ブランチ（`mywork`）に戻り、**同じ先頭行**を別の内容に書き換えてコミットします。

```bash
git switch mywork
# memo.txtの先頭行を、conflict-branchとは違う内容に編集
git add memo.txt
git commit -m "myworkで先頭行を変更"
```

マージしてみます。

```bash
git merge conflict-branch
```

コンフリクトが発生し、`memo.txt` の中に次のようなマーカーが挿入されます。

```
<<<<<<< HEAD
myworkでの内容
=======
conflict-branchでの内容
>>>>>>> conflict-branch
```

ファイルを開き、マーカー（`<<<<<<<` / `=======` / `>>>>>>>`）を削除しつつ、残したい内容に手で編集してください。<br>
編集が終わったら、通常のコミット手順で解消を確定します。

```bash
git add memo.txt
git commit -m "コンフリクトを解消"
```

---

## 9. stash

**目的: 作業途中の変更を一時退避・復元する方法を覚える**

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

## 10. 取り消し系（restore / reset / revert）

**目的: restore / reset / revert の違いと使い分けを理解する**

それぞれ「取り消せる範囲」が違います。<br>
1つずつ試してください。

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

`revert` は過去のコミットを削除するのではなく、「打ち消すための新しいコミット」を追加する点が `reset` と異なります。<br>
履歴が消えずに残ることを `git log` で確認してください。

---

## 11. fetch / pull / push

**目的: リモートとの間で変更をやり取りする一連の流れを体験する**

**2** で自分専用のブランチを作ってあるので、`origin` の自分用ブランチにそのままpushして構いません。<br>
共有の `main` には触れず、自分のブランチだけをリモートに送ります（実際の開発でも同じ進め方です）。

**push**

`memo.txt` を編集してコミットし、自分のブランチをリモートに送ります。<br>
（`mywork` ブランチのままでOKです）

```bash
# memo.txtを編集
git add memo.txt
git commit -m "push練習用の変更"
git push -u origin mywork
```

初回だけ `-u`（`--set-upstream`）を付けておくと、<br>
以降は `git push` / `git pull` だけで同じ相手とやり取りできるようになります。

**確認すること**

```bash
git branch -vv
```

`mywork` が `[origin/mywork]` を追跡している状態になっていることを確認します。

**「別の作業者」役の作業コピーを用意する**

`fetch` / `pull` は「他の人がリモートを更新した」状況がないと体験できません。<br>
そこで、もう1つ作業コピーを作り、自分で別の作業者役を演じます。<br>
`git-practice` フォルダの外側で作業します。

最初にクローンしたフォルダと別フォルダでクローンします。<br>
（git-practiceのリポジトリを同PCに2つクローンする）

```bash
cd ..
git clone <リポジトリの場所> colleague-copy
cd colleague-copy
git switch mywork
```

> `<リポジトリの場所>` は **1. クローン** のときと同じものです。<br>
> `git switch mywork` で、さきほどpushしたブランチに切り替わることを確認してください。

同僚役として `memo.txt` を編集し、コミットしてpushします。

```bash
# memo.txtを編集
git add memo.txt
git commit -m "別作業者役からの変更"
git push origin mywork
```

**fetch**

元の `git-practice` 側（自分）に戻ります。

```bash
cd ../git-practice
git fetch origin
```

まだ自分の `mywork` ブランチには変更が反映されていないことを確認します。

```bash
git log --oneline mywork
git log --oneline origin/mywork
```

`origin/mywork` の方だけ、新しいコミットが増えていることを確認してください。<br>
（`fetch` は取ってくるだけで、自分のブランチには取り込まない、という点がポイントです）

**pull**

```bash
git pull origin mywork
git log --oneline mywork
```

`pull` を実行すると `fetch` + 取り込み（マージ）がまとめて行われ、自分の `mywork` にも変更が反映されることを確認してください。

> 練習が終わったら、`colleague-copy` フォルダは削除して構いません。

---

## 12. ログの見方

**目的: ブランチ・マージの履歴をグラフで俯瞰する方法を知る**

最後に、ここまでの一連の履歴を俯瞰してみます。

```bash
git log --oneline --graph --all
```

ブランチの分岐、マージ、コンフリクト解消のコミットなどが、どのような形で履歴に残っているかを確認して終了です。
