# pmm-editor

[日本語](README.md) / [English](README-en.md)

本资料用于处理 MMD/PMM 与 MME/EMM。除文件格式外，还汇总了复制、安全性检验、外部亲编辑、动作的应用与提取、修复、优化、打包等操作步骤。

也用于 [PmmFileManager](https://booth.pm/ja/items/8052153) 的 IO 库。

## 目录

| No. | 标题 | 内容 |
| --- | --- | --- |
| 1 | [目录](docs-zhcn/01-index.md) | 文档一览、处理请求的方法 |
| 2 | [基础知识](docs-zhcn/02-basics.md) | 术语、文件类型、字符编码 |
| 3 | [PMM 文件格式](docs-zhcn/03-pmm-format.md) | PMM 的 byte 结构 |
| 4 | [EMM 文件格式](docs-zhcn/04-emm-format.md) | EMM 的文本结构 |
| 5 | [VMD 文件格式](docs-zhcn/05-vmd-format.md) | 动作文件的结构 |
| 6 | [PMX 的参照部分](docs-zhcn/06-pmx-reference.md) | 由 PMX 生成 PMM 中各列表的方法 |
| 7 | [文件之间的关系](docs-zhcn/07-relations.md) | PMM 与 EMM、PMX、VMD 的对应 |
| 8 | [MMD 的行为](docs-zhcn/08-mmd-behavior.md) | 路径的查找方式、PMM 打开方式的差异 |
| 9 | [文件内操作](docs-zhcn/09-operations.md) | 复制、删除、外部亲、动作、修复、新建 |
| 10 | [安全性检验](docs-zhcn/10-validation.md) | 结构、上限、路径、数值的检查 |
| 11 | [打包](docs-zhcn/11-dependencies.md) | 依赖关系（含二次引用）、引用的追踪方法、收集文件的布局 |
| 12 | [迁移](docs-zhcn/12-migration.md) | 将工程移到别处、改写路径 |
| 13 | [问题与步骤的对应](docs-zhcn/13-question-guide.md) | 根据请求示例选择步骤的表 |

## 验证记录

[test](test/verification-en.md)（英文）

## 权利声明

本文档是调查 MMD/MME 文件格式的非官方资料，与两位作者没有关系。
感谢樋口優先生 (MMD) 与舞力介入P先生 (MME).

## 许可证

MIT License<br>
Copyright (C) 2026 hato
