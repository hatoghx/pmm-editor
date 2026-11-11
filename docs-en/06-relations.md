# File Relationships

## 1. Overview

A PMM is used together with EMM, PMX and VMD files. This document describes the rules that connect them.

| Pair | Chapter |
| --- | --- |
| PMM and EMM | 2 |
| PMM and PMX | 3 |
| PMM and VMD | 4 |

## 2. PMM and EMM

### 2.1 Location and Name

The EMM is placed in the same folder as the PMM, with the same name and a different extension. The PMM does not record the name or path of the EMM. The two files are linked only by having the same name.

| PMM | Matching EMM |
| --- | --- |
| `C:\MMD\scene\dance.pmm` | `C:\MMD\scene\dance.emm` |

### 2.2 Automatic Loading and Saving

| Action | What MME does |
| --- | --- |
| Open a PMM | Loads the EMM with the same name, if present |
| Save a PMM | Saves an EMM with the same name when automatic saving of assignment files is on. It is on by default |
| MME "Save settings" | Saves an EMM under any name |
| MME "Load settings" | Loads any EMM |

### 2.3 Object Numbers

The numbers in `[Object]` (`Pmd<number>`, `Acs<number>`) are a single sequence starting at 1, assigned in MMD's drawing order. Models and accessories share the sequence. They can be derived from the PMM as follows.

1. Sort the accessories by render order (field 4 of PMM 8.2), ascending.
2. Sort the models by render order (field 14 of PMM 5.1), ascending.
3. Let k be the post-draw start position (field 28 of PMM 9.1).
4. Concatenate the following and number them 1, 2, 3, and so on from the start.
   1. Accessories whose render order is smaller than k
   2. All models
   3. Accessories whose render order is k or greater

### 2.4 Numbering Example

Three accessories, two models, and a post-draw start position of 1:

| Kind and render order in the PMM | EMM key |
| --- | --- |
| Accessory, render order 0 | `Acs1` |
| Model, render order 1 | `Pmd2` |
| Model, render order 2 | `Pmd3` |
| Accessory, render order 1 | `Acs4` |
| Accessory, render order 2 | `Acs5` |

### 2.5 Difference from PMM Model IDs

EMM numbers are not the PMM model IDs (field 1 of PMM 5.1) or accessory IDs. Model IDs start at 0 and are unrelated to render order. EMM numbers cannot be derived directly from PMM model IDs.

### 2.6 Matching on Load

When MME loads an EMM, it links the `[Object]` lines to the objects in the scene by path, not by number.

1. Compare only objects of the same kind (model or accessory).
2. Compare the paths as written, then converted to absolute paths, then resolved from the EMM folder. Case is ignored. A path that matches the tail of the other is also treated as equal.
3. If nothing matches, compare only the file name and extension.
4. Objects that still have no match are listed to the user.

After matching, object names such as `Pmd2` in `[Effect]` and other sections refer to the matched object.

### 2.7 When the Numbers Drift

The EMM and the PMM may be saved at different times, so the EMM numbering may not match the PMM.

| Situation | Result |
| --- | --- |
| Render order was changed and only the PMM was saved after the EMM | The numbering no longer follows the PMM render order |
| A model or accessory was replaced | The `[Object]` path no longer matches the PMM path |
| The same model file was loaded more than once | Matching by file name alone cannot tell which object is meant |

Because matching uses paths, assignments survive when only the numbering drifts. When a path no longer matches, the assignments of that object are lost.

### 2.8 EMM Changes Needed When Editing a PMM

| Change in the PMM | Required change in the EMM |
| --- | --- |
| Save under a different file name | Save the EMM under the same name |
| Change the path of a model or accessory | Change the path of the same object in `[Object]` |
| Add or delete objects, or change render order | Renumber with the rule of 2.3 and rewrite the object names in every section, including `Owner` lines |
| Change the post-draw start position | Same as above |

To renumber, load the EMM, reassign numbers per object, and write it out again. Replacing text in lines would also change characters inside paths such as `C:\MMD\Pmd3\stage.fx`.

## 3. PMM and PMX

### 3.1 What the PMM Copies from the PMX

For each model, the PMM holds the bone names, morph names, IK list, list of bones that can have an outside parent, and the number of display frames. How they are built is described in chapter 4 of [PMX Reference](05-pmx-reference.md). Keyframes follow the indices of these lists, so if a list disagrees with the PMX, keys end up on different bones.

### 3.2 Matching When MMD Opens a PMM

When MMD opens a model in a PMM, it loads the PMX from the stored path and compares it with the lists in the PMM.

| Item compared | When it matches | When it does not match |
| --- | --- | --- |
| Bone count and names | Read by index | Bones are matched by name. Keys of names missing from the PMX are discarded |
| Morph count and names | Read by index | Morphs are matched by name. Keys of names missing from the PMX are discarded |
| Number of display frames | The open state is used | The open state is discarded |

When the lists do not match, MMD shows a confirmation saying that the model structure differs from the one at the time the PMM was saved.

### 3.3 Replacing the Model File

If the model is replaced with another model that has the same bone names, the motion carries over through the matching in 3.2. Keys of bones and morphs whose names do not match are lost.

## 4. PMM and VMD

### 4.1 Field Correspondence

| PMM field | VMD field | Notes |
| --- | --- | --- |
| Bone keys (PMM 5.2) | Bone keys (VMD chapter 3) | Write the bone name instead of the bone index. Reorder the interpolation curves |
| Morph keys (PMM 5.3) | Morph keys (VMD chapter 4) | Write the morph name |
| Display, IK and outside parent keys (PMM 5.4) | Display and IK keys (VMD chapter 8) | Outside parents are not stored in VMD |
| Camera keys (PMM 6.2) | Camera keys (VMD chapter 5) | Change the order inside each interpolation curve. The camera parent is not stored in VMD |
| Light keys (PMM 7.2) | Light keys (VMD chapter 6) | |
| Self shadow keys (PMM 11.2) | Self shadow keys (VMD chapter 7) | |
| Accessory keys (PMM 8.3) | None | Cannot be expressed in VMD |
| Gravity keys (PMM chapter 10) | None | Cannot be expressed in VMD |

### 4.2 Name Length

VMD bone and morph names are limited to 15 bytes; PMM allows 19 bytes. Names of 16 to 19 bytes are truncated when written to VMD and may not match the original bone when loaded. MikuMikuMethods inserts keys only into bones and morphs whose names match exactly.

### 4.3 Encoding

VMD names are written in the ANSI code page of the environment where the model is used. To create a VMD for a Japanese environment from a PMM made in a Chinese environment (GBK), read the names as GBK and write them as CP932. Names containing characters that CP932 cannot represent cannot be converted.
