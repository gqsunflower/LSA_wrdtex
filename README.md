# LSA_wrdtex

[LanguageLearningApp](https://github.com/gqsunflower/LanguageStudyApp) が読み込む単語・例文データを保管するリポジトリ。

アプリは起動時ではなく、ユーザーが「コンテンツを同期」を押したタイミングで
`manifest.json` → そこに列挙された各CSVファイル、の順に
`https://raw.githubusercontent.com/gqsunflower/LSA_wrdtex/main/...` から取得し、
端末内のRoomデータベースへ追加・更新する(取得できない場合はエラーを表示するだけで、
既存のアプリ内データはそのまま使える)。

## ファイル構成

- `manifest.json` — 同期対象ファイルの一覧。ここに新しいCSVを追加すれば、
  アプリ側のコードを変更・再配布しなくても次回同期時に反映される。
- `vocab/*.csv` — 単語帳データ。列: `language,term,traditionalTerm,pinyin,meaning,exampleSentence,exampleTranslation,exampleKeyTerms,category,genre`
- `grammar/chapters.csv` — 文法×単語クロス表の章。列: `language,chapterKey,title,summary,sortOrder,chapterType,level`
  `chapterKey`は同期のたびに同じ章を上書き更新するための安定した識別子(言語をまたいで一意)。
- `grammar/examples.csv` — クロス表の例文。列: `chapterKey,genre,sentence,translation,structure`
  `chapterKey`は`chapters.csv`のものと対応させる。`structure`はSVOC等の文型分解(例: `S:We|V:have decided`)、無ければ空欄。

CSVはダブルクォートで囲めばフィールド内にカンマを含められる(標準的なCSVクォート規則、`""`でエスケープ)。

## 更新のしかた

CSVを編集してpushするだけ。既存の単語は`language`+`term`、既存の章は`chapterKey`、
既存の例文は`chapterKey`+`genre`+`sentence`の組み合わせで重複を判定し、
新規なら追加・既存なら内容を更新する(例文は完全一致なら追加しない)。
