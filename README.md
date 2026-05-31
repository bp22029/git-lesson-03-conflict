# 03 コンフリクト練習

## 学習目標

この演習では、別々のブランチで同じファイルの同じ行を変更し、merge 時にコンフリクトが起きることを体験します。

終了後、次のことを説明できるようになることを目標にします。

- なぜコンフリクトが起きるのかを説明できる
- コンフリクトマーカーを見つけられる
- VS Code で内容を選び、手動で修正できる
- コンフリクト解消後に commit できる

## 前提条件

- Git の基本操作を実行できる
- `git switch -c` と `git merge` を使ったことがある
- VS Code で Markdown ファイルを編集できる

## このリポジトリで使うファイル

```text
conflict_note.md
branch_instructions/feature-a.md
branch_instructions/feature-b.md
```

## ブランチの考え方

```text
main
  |
  o
   \
    develop
      |
      o----------------o
       \              /
        feature/a    feature/b
          同じ行を別々に変更
```

この演習では、`conflict_note.md` の同じ行を `feature/a` と `feature/b` の両方で変更します。そのため、後から merge するブランチで必ずコンフリクトが発生します。

## 手順

### 1. develop ブランチを作成する

```bash
git switch -c develop
```

確認ポイント:

- `On branch develop` と表示されるか確認します。

```bash
git status
```

### 2. feature/a ブランチを作成する

```bash
git switch -c feature/a
```

### 3. feature/a で同じ行を変更する

VS Code で `conflict_note.md` を開き、次の行を探します。

```text
結論: このデータから分かることをここに書く。
```

その行を次のように変更します。

```text
結論: 条件Aでは測定値が安定している。
```

保存したら commit します。

```bash
git status
```

```bash
git add conflict_note.md
```

```bash
git commit -m "条件Aの結論を追加"
```

### 4. develop に戻る

```bash
git switch develop
```

確認ポイント:

- `conflict_note.md` の結論行が元に戻って見えることを確認します。
- これは `feature/a` の変更がまだ `develop` に入っていないためです。

### 5. feature/b ブランチを作成する

```bash
git switch -c feature/b
```

### 6. feature/b で同じ行を別の内容に変更する

VS Code で `conflict_note.md` を開き、同じ行を次のように変更します。

```text
結論: 条件Bでは測定値にばらつきがある。
```

保存したら commit します。

```bash
git status
```

```bash
git add conflict_note.md
```

```bash
git commit -m "条件Bの結論を追加"
```

### 7. develop に feature/a を merge する

```bash
git switch develop
```

```bash
git merge feature/a
```

確認ポイント:

- この merge は通常成功します。
- `conflict_note.md` の結論行が条件Aの内容になります。

### 8. develop に feature/b を merge してコンフリクトを起こす

```bash
git merge feature/b
```

確認ポイント:

- `CONFLICT` という表示が出ます。
- `Automatic merge failed` のような表示が出ます。
- これは、この演習では期待される結果です。

### 9. コンフリクトマーカーを確認する

VS Code で `conflict_note.md` を開くと、次のような表示があります。

```text
<<<<<<< HEAD
結論: 条件Aでは測定値が安定している。
=======
結論: 条件Bでは測定値にばらつきがある。
>>>>>>> feature/b
```

意味:

- `<<<<<<< HEAD` から `=======` までは現在の `develop` 側の内容
- `=======` から `>>>>>>> feature/b` までは取り込もうとしている `feature/b` 側の内容

### 10. 内容を手動で修正する

今回は、両方の内容を残して次のように修正します。

```text
結論: 条件Aでは測定値が安定しており、条件Bでは測定値にばらつきがある。
```

注意:

- `<<<<<<< HEAD`
- `=======`
- `>>>>>>> feature/b`

これらの行はすべて削除します。

### 11. コンフリクト解消を commit する

```bash
git status
```

確認ポイント:

- `both modified: conflict_note.md` または conflict 中であることが表示されます。

```bash
git add conflict_note.md
```

```bash
git commit -m "結論行のコンフリクトを解消"
```

確認ポイント:

- commit が成功すれば、コンフリクト解消は完了です。

## 期待される結果

- `git merge feature/b` でコンフリクトが発生する
- `conflict_note.md` にコンフリクトマーカーが表示される
- マーカーを削除し、結論行を1つの自然な文章に直せる
- コンフリクト解消後の commit が作成される

## よくあるエラー

### コンフリクトが起きない

原因:

同じ行を変更していない可能性があります。

確認すること:

- `feature/a` と `feature/b` の両方で、`結論:` から始まる同じ行を変更したか
- それぞれのブランチで commit したか
- `develop` に `feature/a` を merge してから `feature/b` を merge したか

### `error: you need to resolve your current index first`

原因:

コンフリクトを解消しないまま、別の操作をしようとしています。

対応:

```bash
git status
```

表示された conflict ファイルを修正し、`git add` と `git commit` を行います。

### コンフリクトマーカーが残っている

原因:

修正後のファイルに `<<<<<<<`、`=======`、`>>>>>>>` が残っています。

確認すること:

VS Code の検索で次を探してください。

```text
<<<<<<<
```

残っていたら削除してから commit します。

## 提出物

- コンフリクトが発生したときの `git status` の記録
- 修正後の `conflict_note.md`
- コンフリクト解消 commit の URL
- 授業中の作業記録

## 振り返り質問

- なぜ `feature/b` を merge したときにコンフリクトが起きましたか。
- `HEAD` 側の内容は、どのブランチの内容でしたか。
- 実際の共同研究でコンフリクトを減らすには、どのような工夫ができそうですか。

## 提出前チェックリスト

- [ ] `feature/a` で結論行を変更した
- [ ] `feature/b` で同じ結論行を別の内容に変更した
- [ ] `develop` に `feature/a` を merge した
- [ ] `develop` に `feature/b` を merge してコンフリクトを確認した
- [ ] コンフリクトマーカーを削除した
- [ ] 修正後に `git add conflict_note.md` を実行した
- [ ] コンフリクト解消 commit を作成した
- [ ] 作業記録を書いた
