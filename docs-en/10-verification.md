# Verification

## 1. Overview

This document collects the evidence for the other documents. The format and procedure documents state conclusions only; counts and differences from existing implementations are kept here.

## 2. Sources

| Source | Author and license | How it was used |
| --- | --- | --- |
| PmmFileManager 3.4.1 source code | hato | Read the procedures for duplication, deletion, transplanting, collecting, relinking, VMD extraction and repair |
| MikuMikuMethods and MmdAssetMethods, which is based on it | Inwerwm, MIT | Read the PMM, VMD and EMM readers and writers and the PMX-to-PMM conversion |
| Manual bundled with MikuMikuEffect | Buryoku Kainyuu P | Read the description of automatic loading and saving of EMM and EMD |
| Real PMM, EMM and PMX files | Test files prepared by the author | Read with a parser and compared with the specification |

No code or text from the sources was copied. The test files are not included in this repository.

## 3. Methods

### 3.1 PMM

1. Built a parser that reads from the start according to the specification and checked that it consumes exactly every byte of the file.
2. Checked that the chain rules (PMM 2.3) hold for every keyframe list.
3. Collected value distributions and checked that they agree with the names given by the sources.

### 3.2 EMM

1. Classified and counted every line (header, value, blank, comment).
2. For pairs where a PMM with the same name exists in the same folder, compared the `[Object]` numbers and paths with the models and accessories in the PMM.

### 3.3 PMX

For the PMX files referenced by PMMs that were available locally, compared bones, morphs, IK, bones that can have an outside parent, and the number of display frames.

## 4. Results

### 4.1 PMM

| Item | Result |
| --- | --- |
| Files | 134 (about 0.03 MB to 48 MB) |
| Read to the last byte | 134 |
| Models contained | 1,613 |
| Accessories contained | 1,077 |
| Bone key chains | Valid in all 1,605 models |
| Morph key chains | Valid in all 1,528 models |
| Other key chains | Valid in all 130 files |

### 4.2 EMM

| Item | Result |
| --- | --- |
| Files | 36 |
| `[Info]`, `[Object]`, `[Effect]` | Present in all 36 |
| `Version` | 3 in all 36 |
| `[Effect@name]` | 571 sections, all starting with an `Owner` line |
| Line breaks | CR LF in 35, LF only in 1 |
| Encoding | CP932 in 31, GBK in 5 (saved on Chinese Windows) |

### 4.3 PMM and EMM Pairs

| Item | Result |
| --- | --- |
| Pairs | 32 pairs with the same name |
| Exact match with the numbering rule (Relations 2.3) | 31 pairs |
| Mismatch | 1 pair. The render order of two models and two accessories was swapped between the EMM and the PMM, apparently because they were saved at different times |

PmmFileManager computes EMM numbers with the same rule.

### 4.4 PMM and PMX

| Item | Result |
| --- | --- |
| Models | 115 models whose PMX was available |
| Bone name list | Exact match with the PMX in 110 models. The other 5 match after converting the PMX names to CP932 and truncating them to 19 bytes |
| Morph name list | Exact match in 105 models. In 9 models the count matches but some names differ (every case examined was truncation to 19 bytes or loss of characters not representable in CP932). In 1 model the count differs |
| IK bone list | Matches the bones with the IK bit in all 115 models |
| Bones that can have an outside parent | Matches the condition of PMX chapter 4 in all 110 models whose bone names match exactly |
| Number of display frames | Of the same 110 models, PMX display frames + 1 in 94 and equal to the PMX display frames in 16 |

Bone and morph names were 19 bytes or shorter in all 1,613 models.

## 5. Observed Values

### 5.1 PMM

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
| PMM 5.5 physics off, unknown, selected | 179, 0 and 2,890 entries are 1 |
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

### 5.2 EMM

| Specification | Observation |
| --- | --- |
| EMM 5.2 paths | 803 absolute, 76 relative |
| EMM 7.1 whole-object lines | 10,329 paths, 2,989 `none` |
| EMM 7.1 material lines | 1,492 paths, 20 `none` |
| EMM 7.1 `.show` | 1,075 `true`, 11,997 `false` |
| EMM 8.2 `(n)` in names | 129 of 571 sections |
| EMM 8.3 `Owner` forms | 460 object names only, 111 with `@name`, 0 with `[n]` |

## 6. Unconfirmed Items

| No. | Location | Item | Status |
| --- | --- | --- | --- |
| 1 | PMM chapter 4 | Meaning of each of the 7 editor state bytes | Follows the names used by MikuMikuMethods |
| 2 | PMM 5.5 | The two physics-related bytes of the current bone state | Meaning not settled. The second is always 0 |
| 3 | PMM 6.2 | Meaning of 1 in the perspective byte | No example of 1 |
| 4 | PMM 9.2 | Bone operation mode values 2 and above | Mapping of values to operations not settled. Only 0 and 2 observed |
| 5 | PMM 9.3 | The 64 bit of the background video state | Appears only in files with an empty path |
| 6 | PMM 11.3 | Conversion formula for the self shadow range | Inferred only from the default value |
| 7 | PMM chapter 12 | Meaning of field 7 | Always 0 |
| 8 | PMM 2.3 | Non-zero previous values of initial records | Meaning unknown |
| 9 | PMM 5.4 | Outside parent model ID −2 | Described as the ground, but not observed |
| 10 | PMM 2.1 | str8 strings of 128 bytes or more | No examples. The length is taken as 1 byte |
| 11 | PMM 5.8 | When the number of display frames equals the PMX display frames | Observed in 16 models. The condition is unknown |
| 12 | PMM Appendix A | Version 0001 | No real files |
| 13 | EMM 8.3 | The `[n]` form of `Owner` | Not observed |
| 14 | Behavior (all) | MMD behavior | Not confirmed on a real MMD |
| 15 | Relations 2.7 | EMM matching when the same model file is loaded more than once | Not confirmed which model is matched |
| 16 | Procedures 6.3 | Recovery from a truncated PMM | Built from the specification. Not tried on real files |

## 7. Differences from Existing Implementations

Differences found by comparing with real files. The code has not been changed.

### 7.1 MmdAssetMethods (MikuMikuMethods family)

| No. | Location | Real files | MmdAssetMethods |
| --- | --- | --- | --- |
| 1 | PMM 8.4 current accessory state | Order is rotation, scale, position | Reads in the order position, rotation, scale |
| 2 | PMM 6.3 current camera | Eye offset X, Y and distance | Reads them as the target position |
| 3 | PMM 9.3 background video state | A video with a path has 1 | Treats only 64 as having a video |
| 4 | PMM 11.3 self shadow range | Default is 0.01125 | Uses 8875 as the default |
| 5 | PMM 5.6 outside parent references | Nonexistent model IDs remain | Throws an exception when the model is not found |
| 6 | PMM 7.2 second vec3 of the light | Direction | Calls it position |
| 7 | PMM 2.1 length of str8 | 1 byte | Reads a 7-bit encoded integer. Identical for 127 bytes or less |
| 8 | PMX chapter 4 bones that can have an outside parent | IK bones, or bones with translatable, visible and operable | When building from PMX, uses only translatable bones |
| 9 | PMM 5.8 number of display frames | Usually PMX display frames + 1 | When building from PMX, uses the PMX display frame count |

Items 1 and 2 do not change the result when values are read and written back as is, but they give different results when the values are used in calculations. Item 9 only makes MMD discard the open state; loading does not fail.

### 7.2 PmmFileManager

| No. | Location | Description |
| --- | --- | --- |
| 1 | Procedures 2.5 | When deletion removes the `Owner` of a tab, the tab is kept with `Owner = none`. Under MME's checks (EMM chapter 9), an EMM whose `Owner` is not in `[Object]` is not loaded |
