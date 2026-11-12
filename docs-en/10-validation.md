# Safety Validation

## 1. Overview

This document gives the procedure for checking whether a PMM is intact, whether MMD can open it safely, and whether it is fine to write out. The checks fall into four kinds: structure, limits, paths and values. How to repair the problems found is in chapter 6 of [In-File Operations](09-operations.md).

Report the result in three levels.

| Level | Meaning |
| --- | --- |
| Error | The file cannot be read as a PMM, or loading stops partway |
| Warning | The file loads, but a problem appears in MMD, or a value exceeds what MMD can handle |
| Normal | No problem |

## 2. Flow

1. Run the structure checks of chapter 3 from the start in order. If there is an error, limit the later checks to the part that could be read.
2. Run the limit checks of chapter 4. Measure names and counts after the encoding is decided (Procedures 2.2).
3. Run the path checks of chapter 5.
4. Run the value checks of chapter 6.
5. Report the result level by level (chapter 7).

Do not modify the original. When repairing, write out under another name (Procedures 2.1).

## 3. Structure Checks

"MMD can load it" and "MMD crashes" are results tried on MMD 9.32 (Verification record).

| No. | Check | Condition | Level and repair when it fails |
| --- | --- | --- | --- |
| 1 | Signature | The first 24 bytes are `Polygon Movie maker 0002` (or `0001`) and the next byte is 0 | Error. Not a PMM, or an unsupported version (PMM chapter 4, Appendix A) |
| 2 | Position after reading | The PMM is read to the last byte and the position equals the file size. A file that ends at chapter 12 and a file that has chapter 13 are both normal | Error when the position is smaller than the size (truncated; Procedures 6.3). Warning when it is larger (extra bytes follow; MMD can load it) |
| 3 | Counts | The model, bone, morph, IK, outside-parent slot, accessory and key counts are 0 or more and fit in the remaining bytes | Error. The count bytes are corrupted (Procedures 6.1) |
| 4 | Key chains | The frame of each initial record is 0. Following next visits frames in ascending order. Added record IDs are ascending and not smaller than the number of initial records. The last next is 0. The previous of the first added record is the ID of the initial record (PMM 2.3) | Warning. Keys are missing or out of order in MMD (Procedures 6.4) |
| 5 | Model IDs | No duplicates within the PMM | Error. References cannot be resolved (PMM 5.8) |
| 6 | Selected model and camera key parent | The selected model in the header and the parent model ID of the camera keys exist in the PMM (except −1) | Warning. The target does not exist. MMD can load it |
| 7 | Current outside-parent state and camera follow target | The parents in the current outside-parent state (PMM 5.6) and the camera follow target (PMM chapter 12) point to a model in the PMM and to an existing bone of that model (none is −1) | Error. MMD crashes (Procedures 4.1) |
| 8 | Index ranges | The selected bone and morphs are −1 or in the range 0 to count − 1. IK bone indices and the indices of bones that can have an outside parent are 0 to bone count − 1 (the first outside-parent index is −1) | Warning. An index points outside the range. MMD can load it |
| 9 | Outside-parent pairs in keys | (−1, 0), (−2, 0), or an existing model with an existing bone (PMM 5.4) | Warning. A combination MMD does not accept (Procedures 4.1) |
| 10 | Display frame count | Item 5 equals the number of PMX display frames or is 1 larger. Item 5 and item 18 have the same value, which is 255 or less (PMM 5.8) | Error. MMD crashes when item 5 does not fit |
| 11 | Render order | The render order of models runs 1, 2, 3, and so on. The render order of accessories runs 0, 1, 2, and so on | Warning. The EMM numbers drift (Relations 2.3) |
| 12 | Model lists | The bone names, morph names, IK list and outside-parent bone list match the model file (PMX chapter 4) | Warning. MMD reports that the model structure differs (Relations 3.2) |

## 4. Limit Checks

Count names and counts in the code page of the PMM. A bone or morph name over 19 bytes no longer matches the name in the model file (Procedures 8.3).

| Target | Limit | Unit | Note |
| --- | --- | --- | --- |
| Model name (Japanese and English) | 49 | bytes | |
| Bone name, morph name | 19 | bytes | Names written to a VMD are 15 bytes (VMD chapter 2, Procedures 5.1) |
| Path (model, accessory, audio, background video, background image) | 255 | bytes | A NUL must fit in fix(256) |
| Model count | 255 | models | Counted in a u8 |
| Accessory count | 255 | accessories | Counted in a u8 |
| Display frame count | 255 | frames | Per model |

When adding models, check first that the count after the addition does not exceed the limit (Procedures 2.6).

## 5. Path Checks

Check the paths of the models, accessories, audio, background video and background image in the PMM on three points.

| Check | Condition | Level when it fails |
| --- | --- | --- |
| Existence | The file is found with the way MMD searches (MMD Behavior 2.2) | Warning. MMD shows a confirmation screen during loading (MMD Behavior 4.1, 4.2) |
| Encoding | Every character of the path can be represented in the code page of the PMM | Warning. Characters that cannot be represented are lost, and the file is no longer found |
| Length | 255 bytes or less | Warning. It does not fit in fix(256) |

Check audio, background video and background image only when they are enabled (PMM 9.1 items 11, 17 and 22).

When an EMM exists, also check the loading checks of the EMM (EMM chapter 9) and that the `[Object]` paths bind to the models and accessories of the PMM (Relations 2.3).

## 6. Value Checks

| Check | Condition | Level when it fails |
| --- | --- | --- |
| Numeric range | Each f32 value is neither NaN nor infinite and its absolute value is below 3.40282 × 10^38 | Warning. Objects fly away, disappear or crash in MMD (Procedures 6.2) |

## 7. Reporting the Result

- For each level, report the target (model name, accessory name, item name), the measured value, the limit or condition, and the section for the repair.
- For limit checks, report the maximum measured value as well as the items over the limit.
- Do not modify the original; show a repair plan. Before repairing, tell the user what will be changed.
