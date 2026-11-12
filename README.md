# pmm-editor

MMD のプロジェクトファイル（PMM）と、MikuMikuEffect の割り当てファイル（EMM）を扱うための資料です。ファイルの形式に加えて、複製、安全性検証、外部親編集、モーション適用と抽出、修復、最適化、パッケージ化などの作業手順をまとめています。

## 読む順番

2 から順に読むと、用語から作業手順までを通して把握できます。依頼に答えるときは、10 から必要な手順をたどれます。

| 番号 | 文書 | 内容 |
| --- | --- | --- |
| 1 | [目次](docs/01-index.md) | 文書の一覧、依頼の進め方 |
| 2 | [基礎知識](docs/02-basics.md) | 用語、ファイルの種類、文字コード |
| 3 | [PMM ファイル形式](docs/03-pmm-format.md) | PMM のバイト構造 |
| 4 | [EMM ファイル形式](docs/04-emm-format.md) | EMM のテキスト構造 |
| 5 | [VMD ファイル形式](docs/05-vmd-format.md) | モーションファイルの構造 |
| 6 | [PMX の参照部分](docs/06-pmx-reference.md) | PMM の一覧を PMX から作る方法 |
| 7 | [ファイル同士の関係](docs/07-relations.md) | PMM と EMM、PMX、VMD の対応 |
| 8 | [MMD の動作](docs/08-mmd-behavior.md) | パスの探し方、PMM の開き方の違い |
| 9 | [作業の手順](docs/09-operations.md) | 複製、削除、外部親、モーション、修復、収集、再リンク、新規作成 |
| 10 | [質問と手順の対応](docs/10-question-guide.md) | 依頼の例から手順を選ぶ表 |

## 検証記録

根拠にした検証の方法と結果は [test](test/verification.md) にあります。

## English

An English version of the documents is available in [docs-en](docs-en/01-index.md).

## 権利表記

MikuMikuDance は樋口優氏、MikuMikuEffect は舞力介入P氏の作品です。この資料は相互運用のために両ソフトのファイル形式を記録した非公式のもので、両氏との関係はありません。

## ライセンス

MIT License

Copyright (C) 2026 hato
