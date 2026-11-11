# EMM File Format

This is an unofficial description of a MikuMikuEffect (by Buryoku Kainyuu P) file format, written for interoperability. It is not affiliated with the author of MikuMikuEffect.

## 1. Overview

EMM is a text file in which MikuMikuEffect (MME below) stores effect assignments. It records which effect file is assigned to which model or accessory.

Terms are defined in [Basics](01-basics.md). The correspondence with PMM and the numbering rule are in [File Relationships](06-relations.md); the evidence is in [Verification](10-verification.md).

## 2. Basic Syntax

### 2.1 Characters and Line Breaks

| Item | Rule |
| --- | --- |
| Encoding | ANSI code page of the system that saved the file. CP932 on Japanese Windows |
| BOM | None. MME aborts loading when a UTF-8 BOM is present |
| Line break | CR LF |
| Line length | Up to 1,023 bytes |

### 2.2 Line Types

| Type | Form | Meaning |
| --- | --- | --- |
| Header | `[name]` | Starts a section |
| Value | `key = value` | Spaces around `=` are optional |
| Blank line | | Separates sections |
| Comment | From `;` or `#` to the end of the line | Ignored when reading |

| Case | How MME handles it |
| --- | --- |
| Spaces at the start and end of a line | Ignored |
| The same key twice in one section | The first line is used |
| A line with an empty value | Loading is rejected |

## 3. Overall Layout

| Order | Section | Required | Meaning |
| --- | --- | --- | --- |
| 1 | `[Info]` | Yes | Format version |
| 2 | `[Object]` | Yes | Models and accessories that can receive effects |
| 3 | `[Effect]` | Yes | Assignments for the main screen |
| 4 | `[Effect@name]` | No | Assignments for each offscreen render target. As many as needed |

Each section is followed by one blank line. A complete example:

```
[Info]
Version = 3

[Object]
Acs1 = C:\MMD\Accessory\floor.x
Pmd2 = C:\MMD\Model\miku.pmx

[Effect]
Default = none
Acs1 = none
Pmd2 = C:\MMD\Effect\main.fx
Pmd2.show = true
Pmd2[3] = C:\MMD\Effect\hair.fx

[Effect@MirrorRT]
Owner = Acs1
Acs1.show = false
Pmd2 = C:\MMD\Effect\mirror_object.fx

```

## 4. [Info]

| Key | Value |
| --- | --- |
| `Version` | Integer. Current MME writes 3 and reads 1 to 3 |

## 5. [Object]

### 5.1 Line Form

| Key | Value |
| --- | --- |
| `Pmd<number>` | Path of a model file |
| `Acs<number>` | Path of an accessory file |

Numbers start at 1 and are shared between models and accessories as one sequence. How they are assigned is described in chapter 2 of [File Relationships](06-relations.md). Keys in this section never carry `[n]` or `.show`.

### 5.2 How Paths Are Written

MME writes paths as follows. Effect file paths (chapter 7) follow the same rule.

| Location of the file | Written as | Example |
| --- | --- | --- |
| The path contains a folder named `UserFile` | Relative path starting at `UserFile` | `UserFile\Model\miku.pmx` |
| Otherwise | Absolute path | `D:\stage\stage.pmx` |

Relative paths starting with `UserFile` are based on the folder that contains the MMD executable. Other relative paths are resolved from the folder that contains the EMM file.

### 5.3 Object Names

Other sections use the key itself, such as `Pmd2` or `Acs1`, as the name of the model or accessory. This document calls it the object name.

## 6. [Effect]

Assignments for the main screen.

| Line | Meaning |
| --- | --- |
| `Default = value` | First line. An effect file path or `none` |
| Assignment lines | Form of chapter 7, one group per object |

## 7. Assignment Lines

### 7.1 Key Forms

| Key | Target | Value |
| --- | --- | --- |
| `<object name>` | The whole object | Effect file path or `none` |
| `<object name>.show` | Visibility of the whole object | `true` or `false` |
| `<object name>[n]` | The n-th material (from 0) | Effect file path or `none` |
| `<object name>[n].show` | Visibility of the n-th material | `true` or `false` |

- `none` means "no effect assigned".
- `.show` accepts only lowercase `true` or `false`.
- Only object names listed in `[Object]` may be used.
- Effect file paths follow 5.2. Extensions are fx, fxsub, fxm and similar.

### 7.2 Order

Write objects in number order, each in the following order. Lines without a value are omitted.

1. `<object name>`
2. `<object name>.show`
3. `<object name>[n]` and `<object name>[n].show` (ascending n; for the same n, the material path comes first)

## 8. [Effect@name]

### 8.1 Purpose

When an effect that uses offscreen rendering is assigned, MME adds a tab to its assignment window. `[Effect@name]` holds the assignments of one such tab.

### 8.2 Name

| Part | Meaning |
| --- | --- |
| Name | Name of the offscreen render target defined by the effect file (letters, digits and `_`) |
| Trailing `(n)` | When several tabs share a name, the second one gets `(1)`, the third `(2)`, and so on |

Examples: `[Effect@PointLightRT]`, `[Effect@PointLightRT(1)]`

### 8.3 Lines

| Line | Meaning |
| --- | --- |
| `Owner = value` | First line. The owner of the effect that created this tab |
| Assignment lines | Form of chapter 7 |

There is no `Default` line.

The `Owner` value takes one of these forms.

| Form | Meaning |
| --- | --- |
| `<object name>` | The tab was created by the effect assigned to that object |
| `<object name>[n]` | The tab was created by the effect assigned to the n-th material of that object |
| `<object name>@<name>` | The tab was created by an assignment inside another tab `[Effect@name]` |

The `Owner` line only identifies the owner; it does not assign anything.

## 9. Load Checks

MME refuses to load an EMM when any of the following is true.

- `[Info]`, `[Object]` or `[Effect]` is missing
- `Version` is missing or outside 1 to 3
- `[Effect]` has no `Default` line
- An `[Effect@name]` has no `Owner` line
- An object name in an `Owner` or assignment line is not in `[Object]`

## Appendix A. Differences from EMD

EMD stores the assignments of a single model or accessory. When an EMD with the same name is placed in the same folder as that file, it is applied automatically on load.

| Item | EMM | EMD |
| --- | --- | --- |
| Scope | Whole scene | One model or accessory |
| Sections | `[Info]`, `[Object]`, `[Effect]`, `[Effect@name]` | `[Info]` and `[Effect]` only |
| `Default` line | Required in `[Effect]` | Not allowed |
| Object names | `Pmd<number>`, `Acs<number>` | `Obj` only |
