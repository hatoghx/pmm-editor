# MMD Behavior

## 1. Overview

This document describes how MMD 9.32 behaves when it opens a PMM.

| Topic | Chapter |
| --- | --- |
| Path resolution | 2 |
| Differences between the three ways of opening a PMM | 3 |
| What happens during loading | 4 |

## 2. Path Resolution

### 2.1 Terms

| Term | Meaning |
| --- | --- |
| Executable folder | The folder that contains MikuMikuDance.exe |
| Project folder | When the path of the opened PMM contains `UserFile`, the folder before it. Otherwise none |
| Current folder | The "current directory" that Windows keeps per process. Relative paths are opened from here |

For example, if the PMM is `D:\work\UserFile\scene\a.pmm`, the project folder is `D:\work\`.

### 2.2 Steps

MMD tries the paths stored in the PMM (models, accessories, audio, background) in the following order and uses the first one that opens.

1. If the path contains `UserFile`:
   1. Executable folder + the part of the path from `UserFile` onward
   2. Project folder + the part of the path from `UserFile` onward
2. Open the path as written. A relative path is based on the current folder
3. If the extension is pmd, wav, x or vac, a file with the same name in `UserFile\Model`, `UserFile\Wave` or `UserFile\Accessory` under the executable folder

### 2.3 Consequences

- For a path that contains `UserFile`, even an absolute one, the `UserFile` folder under the executable folder is searched first. Even if a path points to an old MMD folder, the same asset in the current MMD is used when it exists.
- Whether a relative path without `UserFile` is found depends on the current folder. MMD moves the current folder to the model's folder every time it loads a model, so relative paths of the second and later models may not be found.
- The pmx extension is not covered by step 3 of 2.2.
- A PMM shared with others is less dependent on their environment when its paths are absolute or contain `UserFile`.

### 2.4 Shared Toons

The shared toons of a PMX (chapter 5 of [PMX Reference](06-pmx-reference.md)) and toon numbers 0 to 9 of a PMD use `toon01.bmp` to `toon10.bmp` in the `Data` folder of the MMD executable folder. This is outside the steps of 2.2, and the model loads even when the model folder does not contain these files. Replacing a file in `Data` changes the shading of the materials that use that toon.

## 3. Differences Between the Three Ways of Opening a PMM

### 3.1 Comparison

| Item | Drop on the exe icon | "Open" from the menu | Drop on the window |
| --- | --- | --- | --- |
| MMD instance | Starts a new MMD | Uses the running MMD | Uses the running MMD |
| How the path is received | ANSI string | Unicode string | Unicode string |
| Paths with characters outside the ANSI code page | Cannot be opened | Can be opened | Can be opened |
| Prompt for unsaved changes | None (new MMD) | Yes | Yes |
| Current folder just before loading | Executable folder | Folder of the PMM (the file dialog moves the current folder) | Depends on the previous operation (for example the folder of the last loaded model) |
| Extension check | Contains `.pmm` or `.PMM` | The file dialog limits to pmm | Contains `.pmm`, `.PMM` or `.Pmm` |
| Files per operation | 1 | 1 | Several (processed in order) |

### 3.2 Why They Differ

- When a file is dropped on the exe, Windows passes the path to MMD as an ANSI string. Characters outside the ANSI code page (for example Simplified Chinese characters on Japanese Windows, whose code page is 932) become `?` and the file cannot be opened.
- The menu and dropping on the window receive the path as Unicode.
- How relative paths without `UserFile` are found depends on the current folder (2.3). Because each method leaves a different current folder, the same PMM may or may not find its models depending on how it is opened.

### 3.3 Recommended Method

- Use "Open" from the menu by default. There is no character restriction, and the current folder becomes the PMM folder, so relative paths based on the PMM folder are found as well.
- Dropping on the window has no character restriction either. However, the current folder depends on the previous operation, so results are not stable for a PMM with relative paths.
- Dropping on the exe works when the path contains only characters of the ANSI code page. It saves the step of starting MMD first.

## 4. What Happens During Loading

### 4.1 Model Not Found

MMD shows a dialog and lets the user choose one of the following.

| Choice | Result |
| --- | --- |
| Specify the file | Select another model in a file dialog and load it |
| Skip | Do not load the model; its data in the PMM is read and discarded |
| Abort | Stop loading |

### 4.2 Accessory Not Found

A file dialog is shown. Cancelling it aborts the whole load.

### 4.3 Different Model Structure

What happens when the lists in the PMM disagree with the PMX is described in 3.2 of [File Relationships](07-relations.md).

### 4.4 Loading the EMM

After the PMM has been loaded, MME loads the EMM with the same name (2.2 of [File Relationships](07-relations.md)).
