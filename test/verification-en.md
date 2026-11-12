# Verification

## 1. Overview

This document collects the evidence for the other documents. The format and procedure documents state conclusions only; counts are kept here.

## 2. Methods

### 2.1 PMM

1. Built a parser that reads from the start according to the specification and checked that it consumes exactly every byte of the file.
2. Checked that the chain rules (PMM 2.3) hold for every keyframe list.
3. Collected value distributions and checked that they agree with the meaning of each field.

### 2.2 EMM

1. Classified and counted every line (header, value, blank, comment).
2. For pairs where a PMM with the same name exists in the same folder, compared the `[Object]` numbers and paths with the models and accessories in the PMM.

### 2.3 PMX

For the PMX files referenced by PMMs that were available locally, compared bones, morphs, IK, bones that can have an outside parent, and the number of display frames.

## 3. Results

### 3.1 PMM

| Item | Result |
| --- | --- |
| Files | 134 (about 0.03 MB to 48 MB) |
| Read to the last byte | 134 |
| Models contained | 1,613 |
| Accessories contained | 1,077 |
| Bone key chains | Valid in all 1,605 models |
| Morph key chains | Valid in all 1,528 models |
| Other key chains | Valid in all 130 files |

### 3.2 EMM

| Item | Result |
| --- | --- |
| Files | 36 |
| `[Info]`, `[Object]`, `[Effect]` | Present in all 36 |
| `Version` | 3 in all 36 |
| `[Effect@name]` | 571 sections, all starting with an `Owner` line |
| Line breaks | CR LF in 35, LF only in 1 |
| Encoding | CP932 in 31, GBK in 5 (saved on Chinese Windows) |

### 3.3 PMM and EMM Pairs

| Item | Result |
| --- | --- |
| Pairs | 32 pairs with the same name |
| Exact match with the numbering rule (Relations 2.3) | 31 pairs |
| Mismatch | 1 pair. The render order of two models and two accessories was swapped between the EMM and the PMM, apparently because they were saved at different times |


### 3.4 PMM and PMX

| Item | Result |
| --- | --- |
| Models | 115 models whose PMX was available |
| Bone name list | Exact match with the PMX in 110 models. The other 5 match after converting the PMX names to CP932 and truncating them to 19 bytes |
| Morph name list | Exact match in 105 models. In 9 models the count matches but some names differ (every case examined was truncation to 19 bytes or loss of characters not representable in CP932). In 1 model the count differs |
| IK bone list | Matches the bones with the IK bit in all 115 models |
| Bones that can have an outside parent | Matches the condition of PMX chapter 4 in all 110 models whose bone names match exactly |
| Number of display frames | Of 1,162 models whose PMX was available, PMX display frames + 1 in 1,085 (all with morphs); equal to the PMX display frames in 77 (64 without morphs, 13 with morphs) |

Bone and morph names were 19 bytes or shorter in all 1,613 models.

## 4. Observed Values

### 4.1 PMM

| Specification | Observation |
| --- | --- |
| PMM chapter 4, last 5 bytes of the signature | Non-zero in 42 of 134 files |
| PMM 2.2 encoding | PMM files saved on Chinese Windows are GBK |
| PMM 2.3 frame of initial records | 0 in all 65,916 bone records |
| PMM 2.3 previous of initial records | Non-zero in 14 of 130 files for the camera and 4 for the light |
| PMM 2.3 IDs of additional records | Ascending and not smaller than the number of initial records in all 241 models |
| PMM 5.1 first value of field 13 | −1 in all 1,613 models |
| PMM 5.1 field 18 | Equal to field 5 in all 1,605 models |
| PMM 5.1 field 21 | Equal to the largest key frame in 763 models, larger in 842 |
| PMM 5.2 selected | 3,224 of 65,916 are 1 |
| PMM 5.2 physics off | 196 of 65,916 are 1 |
| PMM 5.4 outside parent (−1, 0) | Bone index is 0 in all 1,678 entries with model ID −1 |
| PMM 5.5 physics off, unregistered change, selected | 179, 0 and 2,890 entries are 1 |
| PMM 5.6 start and end without an outside parent | 9,926 of 9,943 are (0, 0) |
| PMM 5.6 nonexistent model IDs | 74 of 403 entries with ID 0 or more |
| PMM 6.2 perspective | 0 in all 9,066 camera keys |
| PMM 6.2 initial camera key | Distance −45 in 43 of 130 files, position (0, 10, 0) in 41 |
| PMM 6.3 eye offset and distance | Example (0, 0, −15) |
| PMM 8.4 order of the current state | A floor accessory has scale 10.0 and position Y 0.08. Reading it in key order gives a scale of 0 |
| PMM 8.5 visibility and transparency | Values such as 1, 0, 101, 141, 161, 177 and 181 appear; all fit the formula |
| PMM 9.1 field 17 | 0 in 125 files, 1 in 3, 64 in 2, 65 in 4. Only the three files with 1 have a path |
| PMM 9.2 bone operation mode | 0 in 35 files, 2 in 99 |
| PMM 11.3 range | 0.01125 in 122 of 134 files |
| PMM chapter 12, field 5 | Identity in 131 files. The other 3 have a follow target and a rotation-only matrix |
| PMM chapter 12, field 7 | 0 in all 134 files |
| PMM chapter 13, range selection present | 1 in all 134 files |

### 4.2 EMM

| Specification | Observation |
| --- | --- |
| EMM 5.2 paths | 803 absolute, 76 relative |
| EMM 7.1 whole-object lines | 10,329 paths, 2,989 `none` |
| EMM 7.1 material lines | 1,492 paths, 20 `none` |
| EMM 7.1 `.show` | 1,075 `true`, 11,997 `false` |
| EMM 8.2 `(n)` in names | 129 of 571 sections |
| EMM 8.3 `Owner` forms | 460 object names only, 111 with `@name`, 0 with `[n]` |

## 5. Other Verification

| Item | Result |
| --- | --- |
| The 7 editor state bytes | Across 134 files, the first byte is 0 in 47 and 1 in 88; the others are mostly 1. The meanings are in PMM chapter 4 |
| Previous of initial records | Non-zero in 14 camera and 4 light lists. It does not match the last ID of the chain |
| String length | In all strings of the 134 files, the length byte is below 128 |
| Outside-parent model ID | −2 does not appear in the keys of the 134 files (−2 means the ground) |
| Truncated PMM | A PMM with 3 models was cut and read again: at 95% of the length, 2 models were recovered completely with their key counts; at 99%, all 3 |
| 0001 | The three samples shipped with MMD 9.32 were read to the last byte with the structure in Appendix A |

## 6. Verification on MMD 9.32

A PMM was made whose model path was rewritten to a relative path without `UserFile` (a file name only). The PMM and the model were placed in the same folder, outside the MMD folder, and the PMM was opened.

| Way of opening | Result |
| --- | --- |
| Start MMD with the PMM as an argument (working folder is the executable folder) | The "model file not found" screen appears. The current folder is the executable folder, so the relative path is not found (Behavior 2.3, 3.1) |
| Open from the menu "Open" in a running MMD | The model is found and loading continues (the current folder becomes the PMM folder; Behavior 3.1). The PMM and PMX lists of this file differed, so the "model structure differs" screen followed (Relations 3.2) |
| Drop onto the window of an MMD just started (a file dragged from another application window) | The "model file not found" screen appears. No other operation was made, so the current folder is still the executable folder (Behavior 3.1) |
| Drop onto the window after loading a model from the menu | The model is found and loading continues. The current folder remains the folder of the model loaded just before (Behavior 3.1) |

On Japanese Windows (932), a PMM was placed in a folder whose name contains Simplified Chinese characters (characters not in 932), and the ways of opening were compared. The model path is an absolute path.

| Way of opening | Result |
| --- | --- |
| Start MMD with the PMM path as an argument | The "file cannot be loaded: 22" screen appears and the file cannot be opened (Behavior 3.1) |
| Open from the menu "Open" in a running MMD | It opens. The model is found and loading continues (Behavior 3.1) |
