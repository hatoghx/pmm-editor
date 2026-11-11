# Question Guide

## 1. Overview

This table maps user questions and requests to the documents and procedures to use. Every procedure listed here points to a section of another document.

Short section names such as "Procedures 3.1" follow chapter 3 of the [Index](00-index.md).

## 2. Rules for Every Request

1. Do not overwrite the original PMM and EMM; write the result under a different name (Procedures 2.1).
2. Determine the encoding before reading (Procedures 2.2).
3. If an EMM with the same name exists, read it together with the PMM and write them together (Relations 2.1).
4. When a model or bone name does not exactly match the user's words, show the candidates and confirm. Model names often have extra words at the end, such as "YYB式鏡音リン_10th".
5. After writing, read the file again and confirm that it reads to the last byte (Procedures 2.1).
6. Report what was changed with model names, bone names and counts.

## 3. Example Requests and Procedures

### 3.1 "Duplicate the model YYB Kagamine Rin right below the original"

| Item | Content |
| --- | --- |
| Confirm | The target model (when several names match) |
| Procedure | Procedures 3.1 |
| EMM | Always renumber (Procedures 2.5). Ask whether effects should be copied as well; if so, use Procedures 3.2 |
| Notes | The model limit is 255 |

### 3.2 "Remove all outside-parent settings of the center bone of YYB Hatsune Miku"

| Item | Content |
| --- | --- |
| Confirm | The target model. Whether the center bone can have an outside parent (field 13 of PMM 5.1) |
| Procedure | Procedures 4.1 |
| EMM | No change |
| Notes | Clear both the per-key outside parents (PMM 5.4) and the current state (PMM 5.6) |

### 3.3 "I have a Chinese PMM. Give me the motion of this bone of this model as a VMD"

| Item | Content |
| --- | --- |
| Confirm | The encoding (Procedures 2.2; usually GBK), the target model and bone, and the environment where the VMD will be used (Japanese MMD or not) |
| Procedure | Procedures 5.1 |
| Notes | Convert names to the encoding of the target environment (Relations 4.3). VMD bone names are limited to 15 bytes (Relations 4.2) |

### 3.4 "This PMM is broken. Can you at least extract the motion?"

| Item | Content |
| --- | --- |
| Confirm | How it is broken (MMD cannot open it, or it opens but the motion is wrong) |
| Procedure | Identify the damage with Procedures 6.1, then repair or recover with 6.2 to 6.4. Then extract to VMD with Procedures 5.1 |
| Notes | In a truncated file, models after the cut may not be recoverable |

### 3.5 "Duplicate this model together with its effects"

| Item | Content |
| --- | --- |
| Confirm | The target model. Whether an EMM with the same name exists |
| Procedure | Procedures 3.1 and 3.2 |
| Notes | Offscreen tabs owned by the original model are not duplicated (Procedures 3.2) |

### 3.6 "Package this PMM"

| Item | Content |
| --- | --- |
| Confirm | The output folder. The location of the MMD executable folder (to resolve UserFile relative paths) |
| Procedure | Procedures 7.1 and 7.2 |
| Notes | List files that cannot be found. Abort if a path exceeds 255 bytes |

### 3.7 "Create a PMM from scratch. The model is X and the motion is Y"

| Item | Content |
| --- | --- |
| Confirm | Path of the model PMX, path of the motion VMD, whether there is a camera VMD, the output location |
| Procedure | Procedures chapter 8 |
| Notes | Keys whose VMD bone names do not match the PMX are not imported (Procedures 5.3) |

### 3.8 "In MMD, what is the difference between opening a PMM by dropping it on the exe icon, opening it from the menu, and dropping it on the window? Which is best?"

| Item | Content |
| --- | --- |
| Answer | Show the differences with the table of Behavior 3.1 and give the recommendation of Behavior 3.3 |
| Key points | Dropping on the exe passes an ANSI string, so some characters cannot be used. The current folder differs by method, so relative paths are found differently |

## 4. Frequently Asked Questions

| Question | Where the answer is |
| --- | --- |
| MMD says the model cannot be found | Behavior chapter 2 (path resolution), Procedures 7.3 (after moving a project) |
| Effects were lost after moving to another PC | Relations 2.6, 2.7 |
| List the files this PMM uses | Step 1 of Procedures 7.1 and 7.2 |
| Which model is `Pmd3` in the EMM? | Relations 2.3 |
| Effects shifted after changing render order | Relations 2.8, Procedures 3.5 |
| How many models can a PMM hold? | Procedures 2.6 |
| I want the camera motion as a VMD | Procedures 5.2 |
| I want to set or change an outside parent | Procedures 4.2 |
| I want to load a VMD into a model in the PMM | Procedures 5.3 |
| Objects fly away or values look wrong | Procedures 6.2 |
| I want a smaller file | Procedures 9.1 |
| I want to fix a PMM with garbled text | Procedures 2.2, 9.3 |
