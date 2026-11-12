# Migration

## 1. Overview

This document describes how to move a project to another folder, drive or computer and make it open at the destination. It is written as the procedure for an AI agent running on the user's own computer (such as the desktop Claude Code or Codex) that carries out, locally, an instruction like "move this project from this folder to that folder". How to collect and arrange files is in [Packaging](11-dependencies.md), and path checks are in [Safety Validation](10-validation.md).

## 2. Kinds of Migration

| Kind | Meaning | Original files |
| --- | --- | --- |
| Copy | Copy to the destination and keep the originals | Kept |
| Move | Copy to the destination and delete the originals after the check | Deleted last |
| To another computer | The MMD executable folder at the destination differs from the original | As the user specifies |

## 3. What to Confirm with the User

| Item | Example |
| --- | --- |
| What to move | The source folder and the PMM in it (which one, when there are several) |
| Destination | The destination folder. Whether it exists and whether it is empty |
| Kind | Copy or move |
| MMD executable folder at the destination | When moving to another computer |
| When a file with the same name exists | Overwrite, add a number, or stop |
| Scope | Only the project folder, or also shared assets in `UserFile` of MMD |

Do not decide by guessing what cannot be confirmed; ask the user.

## 4. Procedure

1. Examine the original project. List the EMM with the same name as the PMM, the files the PMM uses and their dependencies, with the procedure of chapter 4 of [Packaging](11-dependencies.md).
2. Check the destination. Confirm that the folder is writable, that paths are 255 bytes or less, that file names contain no unusable characters, and that the paths can be represented in the code page of the PMM (chapters 4 and 5 of [Safety Validation](10-validation.md)).
3. Show the plan. Make a list of which files go where and which paths will be rewritten, show it to the user and get agreement. When a file with the same name exists, follow the user's choice from chapter 3.
4. Copy. Do not change the original files. Copy dependencies keeping their relative positions (5.1 of [Packaging](11-dependencies.md)).
5. Rewrite the paths. Rewrite the copied PMM and EMM with the procedure of chapter 5.
6. Check. Read the copied PMM and EMM again to the end and confirm that every path exists (chapters 3 and 5 of [Safety Validation](10-validation.md)). Compare the number and sizes of the copied files with the originals.
7. Only for a move: when there is no problem up to step 6, ask the user whether the originals may be deleted, and delete them only after agreement. Prefer putting them in the recycle bin.

## 5. Rewriting Paths

Rewrite the absolute paths in the PMM and EMM of a project that was moved as a whole folder, so that they point to the new location. PMM paths are written inside fix(256) (PMM 2.1 and 2.2), and EMM paths are written in the form of EMM 5.2. This is not a search for broken paths.

1. Determine the common folder before the move (source root): the deepest folder shared by the PMM location and every absolute path in the PMM and EMM.
2. Determine the folder after the move (destination root).
3. Convert relative EMM paths that start with `UserFile` (EMM 5.2) to absolute paths in this order.
   1. If found in a `UserFile` folder under the source root, use that absolute path and include it in the rewrite.
   2. If found only in the `UserFile` of the MMD executable folder, treat it as a shared asset and do not rewrite it.
   3. If found in neither, stop and show the list.
4. For every absolute path, replace the leading source root with the destination root. If any path does not start with the source root, stop without changing anything and show the list.
5. Write the new PMM and an EMM with the same name.

## 6. When Moving to Another Computer

- When a PMM path contains `UserFile`, the `UserFile` folder under the MMD executable folder at the destination is searched first ([MMD Behavior](08-mmd-behavior.md) 2.2).
- Relative paths in an EMM or EMD are searched from the MMD executable folder at the destination ([EMM File Format](04-emm-format.md) 5.2).
- Shared toons are used from `Data` of the MMD executable folder at the destination, so do not collect them ([MMD Behavior](08-mmd-behavior.md) 2.4).
- Assets that the MMD at the destination does not have are collected and copied.

## 7. What the Agent Must Observe

- Do not change the originals. Rewrite only the copied files.
- Before an operation that cannot be undone, such as deleting or overwriting, confirm with the user.
- Tell the user the result (the number of files copied, the number of paths rewritten, and what was not found).
- When a step fails midway, ask the user whether to clean up the destination or leave it. The original files were not touched, so leave them as they are.
