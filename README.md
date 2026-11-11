# pmm-editor

MikuMikuDance のプロジェクトファイル（PMM）と、MikuMikuEffect の割り当てファイル（EMM）を扱うための資料です。ファイルの形式に加えて、複製、外部親の削除、モーションの取り出し、修復、パッケージ化などの作業手順をまとめています。

## 読む順番

1 から順に読むと、用語から作業手順までを通して把握できます。依頼に答えるときは、9 から必要な手順をたどれます。

| 番号 | 文書 | 内容 |
| --- | --- | --- |
| 1 | [基礎知識](docs/01-basics.md) | 用語、ファイルの種類、文字コード |
| 2 | [PMM ファイル形式](docs/02-pmm-format.md) | PMM のバイト構造 |
| 3 | [EMM ファイル形式](docs/03-emm-format.md) | EMM のテキスト構造 |
| 4 | [VMD ファイル形式](docs/04-vmd-format.md) | モーションファイルの構造 |
| 5 | [PMX の参照部分](docs/05-pmx-reference.md) | PMM の一覧を PMX から作る方法 |
| 6 | [ファイル同士の関係](docs/06-relations.md) | PMM と EMM、PMX、VMD の対応 |
| 7 | [MMD の動作](docs/07-mmd-behavior.md) | パスの探し方、PMM の開き方の違い |
| 8 | [作業の手順](docs/08-operations.md) | 複製、削除、外部親、モーション、修復、収集、再リンク、新規作成 |
| 9 | [質問と手順の対応](docs/09-question-guide.md) | 依頼の例から手順を選ぶ表 |
| 10 | [検証記録](docs/10-verification.md) | 根拠、観測した値、未確定の事項 |

## English

An English version of the documents is available in [docs-en](docs-en/00-index.md).

## 権利表記

MikuMikuDance は樋口優氏、MikuMikuEffect は舞力介入P氏の作品です。この資料は相互運用のために両ソフトのファイル形式を記録した非公式のもので、両氏とは関係ありません。

## ライセンス

MIT License

Copyright (C) 2026 hato
