# Procedures

## 1. Overview

This document describes procedures for modifying PMM and EMM files.

| Task | Section |
| --- | --- |
| Common steps for every task | 2 |
| Duplicating, deleting, reordering and transplanting models and accessories | 3 |
| Changing outside parents and camera follow | 4 |
| Extracting and importing motion | 5 |
| Repairing and recovering damaged PMM files | 6 |
| Packaging (collecting) and relinking paths | 7 |
| Creating a PMM from scratch | 8 |
| Other cleanup | 9 |

References such as "PMM 5.2" point to [PMM File Format](03-pmm-format.md), "EMM chapter 7" to [EMM File Format](04-emm-format.md), and "Relations 2.3" to [File Relationships](07-relations.md).

## 2. Common Steps

### 2.1 Flow

1. Do not overwrite the originals; write the result under a different name.
2. Determine the encoding of the PMM (2.2).
3. Read the PMM to the last byte. Check that the final read position equals the file size.
4. If an EMM with the same name exists in the same folder, read it (Relations 2.1).
5. Make the changes.
6. Rebuild the key chains and model IDs, then write the file (2.3, 2.4).
7. Renumber the EMM and write it under the same name as the PMM (2.5).
8. Read the written file again and repeat the check of step 3.

### 2.2 Determining the Encoding

Neither PMM nor EMM records its encoding. Decide in this order.

1. If the user specifies an encoding, use it.
2. When the PMX of the model is at hand, encode its bone names in each candidate code page (the table in Basics chapter 7), truncate them to 19 bytes, and use the code page that matches the bone names of the PMM.
3. When there is no PMX, read the model and accessory paths of the PMM in each candidate code page and use the code page in which the file at that path exists.
4. When the names and paths contain only ASCII characters so the candidates cannot be narrowed, or when none fits, ask the user.

The presence of broken characters alone does not decide it. In a 1-byte code page (874, 1250 to 1258) every byte is a character.

When writing, use the same encoding as when reading. Rules for converting to another encoding are in 9.3.

### 2.3 Rebuilding Key Chains

After adding or removing keys, rebuild previous, next and the IDs.

1. For each element (bone, morph and so on), sort its keys by ascending frame.
2. Make the key at frame 0 the initial record. Create one if there is no key at frame 0.
3. Set the ID of the initial record to the element index for bones and morphs, and to 0 for the other lists.
4. Give the remaining keys consecutive IDs starting at the number of initial records. For bones, the first ID is the bone count.
5. Set previous of each key to the ID of the preceding key and next to the ID of the following key. The last key has next 0; the initial record has previous 0.
6. Write the count, then the additional records in ascending ID order.

### 2.4 Renumbering Model IDs

After adding or removing models, renumber the model IDs from 0 in write order and update every reference listed in PMM 5.8 to the new IDs. References to a deleted model become (−1, 0) for outside parents, and −1 and 0 for camera and accessory parents.

### 2.5 Renumbering the EMM

1. From the PMM before the change, compute each object's EMM key (`Pmd2` and so on) with the rule of Relations 2.3.
2. From the PMM after the change, compute the new keys with the same rule.
3. Read the EMM and move each object from its old key to its new key. This covers `[Object]`, every line in `[Effect]` and `[Effect@name]`, and `Owner` lines.
4. Remove the lines of deleted objects. Remove a whole tab whose `Owner` was a deleted object. If any tab lacks an `Owner` or points to an object missing from `[Object]`, MME does not load the EMM at all (EMM chapter 9).
5. Write `[Object]` in number order.

Do not replace text inside lines (Relations 2.8).

### 2.6 Limits

| Item | Limit |
| --- | --- |
| Models, accessories | 255 each (counted in u8) |
| Display frames | 255 |
| Path length | 255 bytes (fix(256) needs room for NUL) |
| Bone and morph names | 19 bytes |

## 3. Models and Accessories

### 3.1 Duplicating a Model

The copy is placed directly after the original in the model sequence and in render order.

| Step | Action | Where |
| --- | --- | --- |
| 1 | Check that the model count does not exceed 255 | |
| 2 | Copy the whole block of the original model (names, path, lists, all keys, current state, rendering info) | PMM 5.1 |
| 3 | Insert the copied block directly after the original in the model sequence | PMM chapter 5 |
| 4 | Put it directly after the original in render order and shift the render order of the following models by one | Field 14 of PMM 5.1 |
| 5 | Put it directly after the original in calculation order as well | PMM 5.7 |
| 6 | Renumber model IDs and update references | 2.4 |
| 7 | Add the range selection state of the copy to the extension | PMM chapter 13 |
| 8 | Keep the outside parents of the copy pointing to the same targets as the original | PMM 5.4, 5.6 |
| 9 | Renumber the EMM (2.5) | |

When several models are duplicated at once and they are linked to each other by outside parents, point the outside parents of the copies to the corresponding copies.

### 3.2 Duplicating a Model With Its Effects

Add the following EMM steps to 3.1.

1. In step 1 of 2.5, record the old key of the original model.
2. Add a line for the copy to `[Object]` with the same path as the original.
3. In every `[Effect]` and `[Effect@name]`, copy the lines of the original (including `.show`, `[n]` and `[n].show`), change the key to that of the copy, and insert them right after the original lines.
4. Renumber from step 2 of 2.5 onward.

Tabs owned by the original model (offscreen rendering) are not duplicated. The copy only inherits the assignments inside existing tabs.

### 3.3 Duplicating an Accessory

The flow is the same as 3.1 with these differences.

- Render order counts from 0 (field 4 of PMM 8.2).
- If the original accessory is before the post-draw start position, add the number of copies to the post-draw start position (field 28 of PMM 9.1).
- Accessories have no calculation order and no outside parents.

### 3.4 Deleting Models or Accessories

1. Clear references to the deleted model (2.4).
2. If the selected model is deleted, clear the selection (set it to 0).
3. Remove the block and close the gaps in render order and calculation order.
4. Decrease the post-draw start position by the number of deleted accessories that were before it.
5. Renumber model IDs (2.4).
6. Renumber the EMM and remove the lines of deleted objects (2.5).

### 3.5 Reordering Render Order

1. Rewrite the render order. Number models from 1 and accessories from 0, without gaps.
2. Renumber the EMM (2.5), because changing render order changes the EMM numbers.

### 3.6 Transplanting Models from Another PMM

| Step | Action |
| --- | --- |
| 1 | Check that both the source and the destination have an EMM with the same name |
| 2 | Copy the model blocks from the source and append them to the end of the destination model sequence |
| 3 | In render order, insert them after the model selected in the destination (or at the end if none), keeping their order from the source |
| 4 | In calculation order, append them to the end of the destination, keeping their order from the source |
| 5 | Point outside parents that refer to models transplanted together to the transplanted copies; set the others to (−1, 0) |
| 6 | Renumber model IDs (2.4) |
| 7 | Renumber the destination EMM (2.5) |
| 8 | Copy the lines of the transplanted models from the source EMM to the destination EMM. Lines in tabs whose `[Effect@name]` does not exist in the destination are not copied; tell the user how many were skipped |

## 4. Outside Parents and Camera Follow

### 4.1 Removing All Outside Parents of a Bone

Procedure for removing the outside parent of a bone from every key and from the current state.

1. Find the model by name. If several models have the same name, ask the user which one.
2. Find the index b of the bone name in the bone name list (field 7 of PMM 5.1).
3. Find the position j of b in the list of bones that can have an outside parent (field 13 of PMM 5.1), counting the leading −1. If b is not in the list, the bone cannot have an outside parent and nothing needs to be done.
4. In every display, IK and outside parent key (the initial record and all additional records), set the j-th outside-parent pair to (−1, 0) (PMM 5.4).
5. In the j-th current outside-parent state, set the parent model ID to −1 and the parent bone index to 0 (PMM 5.6). Keep the start and end frames.

To remove the outside parents of every bone of the model, repeat steps 4 and 5 for j from 1 to the end.

### 4.2 Setting an Outside Parent

In steps 4 and 5 of 4.1, write the parent model ID and parent bone index instead of (−1, 0). When the parent model ID is 0 or more, the parent bone must be an existing bone (PMM 5.4).

A model that follows another model through an outside parent should come after that model in calculation order.

### 4.3 Changing the Camera Follow Target

| Target | Where to write |
| --- | --- |
| Follow target per key | Parent model ID and bone index of the camera key (PMM 6.2) |
| Current follow target | Fields 3 and 4 of the color and follow settings (PMM chapter 12) |
| Follow on or off | Field 6 of the color and follow settings |

## 5. Extracting and Importing Motion

### 5.1 Extracting Model Motion to VMD

1. Determine the encoding (2.2). A PMM is written in the code page of the environment where it was created.
2. Find the model and the index of the bone (or morph) to extract.
3. Follow next from the initial record and collect all keys of that bone (PMM 2.3). Include the initial key at frame 0.
4. Convert the keys to VMD bone keys (Relations 4.1). Reorder the interpolation curves as in VMD 3.1.
5. Write the names in the encoding of the user's environment (Relations 4.3). Use 932 for a Japanese MMD. If a name cannot be represented in that code page, tell the user.
6. Check that the bone name does not exceed 15 bytes. If it does, tell the user that the VMD may not attach to the original bone when loaded.
7. Write the model name into the model name field of the VMD header.

To extract a whole model, convert all bones, all morphs and the display and IK keys in the same way.

### 5.2 Extracting Camera Motion to VMD

1. Collect all camera keys and convert them to VMD camera keys. Reorder the interpolation curves as in VMD 5.1.
2. Include light and self shadow keys if needed.
3. Set the model name in the header to `カメラ・照明`.

The camera follow target (parent model and bone) is not stored in VMD.

### 5.3 Importing a VMD into a PMM

1. For each VMD bone name, find a bone with the same name in the model's bone name list. Keys with names that are not found are not imported.
2. If a key already exists at the same frame, overwrite it; otherwise add it.
3. Enable physics (set the physics-off byte to 0).
4. Rebuild the chains (2.3).
5. Set the last frame (field 21 of PMM 5.1) to at least the largest key frame.

## 6. Repairing and Recovering Damaged PMM Files

### 6.1 Identifying the Damage

| Symptom | Example cause | Section |
| --- | --- | --- |
| Loads, but objects fly away, disappear or MMD crashes | Physics diverged and values became NaN or extremely large | 6.2 |
| Reading stops in the middle | The file is truncated or bytes are corrupted | 6.3 |
| Loads, but keys are missing or out of order | The key chain is broken | 6.4 |

### 6.2 Fixing Overflowed Values

Replace the following values with 0.001 when they are NaN, infinite, or have an absolute value of 3.40282 × 10^38 or more.

| Target | Values |
| --- | --- |
| Bones | Translation and rotation of keys and current state |
| Accessories | Position and rotation of keys and current state |
| Camera | Position, offset, rotation and distance of keys and current state |
| Gravity | Acceleration and direction of keys and current state |

Light color, morph weights, edge width, accessory transparency and scale are not touched because they are unrelated to physics.

### 6.3 Recovering Motion from a Truncated PMM

1. Read from the start according to the specification and keep the model blocks that were read. Record where reading stopped.
2. In the model block where reading stopped, keep what was read, in the order bone keys, then morph keys.
3. Extract each readable model to VMD with the procedure of 5.1.
4. If the camera section was reached, extract the camera too with the procedure of 5.2.

If corrupted bytes in the middle prevent further reading, search for the start of the next model block and resume there. A model block starts with the model ID (u8), two length-prefixed names, and a 256-byte path ending in `.pmx` or `.pmd`. Use the path string to locate the block and resume at the position where the name lengths match.

### 6.4 Fixing Key Chains

1. For each element, collect the initial record and the additional records that belong to it. Discard keys whose owner cannot be determined because the chain is broken.
2. Rebuild the chains with the procedure of 2.3.

### 6.5 Checks After Repair

1. Read the written PMM again and confirm that it reads to the last byte.
2. Confirm that every key chain satisfies the rules of PMM 2.3.
3. Confirm that the items of Validation chapters 3 to 6 give no errors or warnings.

## 7. Packaging and Relinking Paths

### 7.1 Packaging (Collecting)

Gather every file used by the PMM into one folder so that the folder can be opened on its own. An example layout. Choose the layout method in chapter 5 of [Dependencies and Layout](11-dependencies.md).

| Output | Contents |
| --- | --- |
| `<output>\<PMM name>.pmm` | PMM with rewritten paths |
| `<output>\<PMM name>.emm` | EMM with rewritten paths |
| `<output>\Files\Models\000\` onward | Models and their dependencies, one 3-digit folder per model |
| `<output>\Files\Accessories\000\` onward | Accessories and their dependencies |
| `<output>\Files\Media\` | Audio, background video, background image |
| Under `<output>\Files\` | Effects referenced by the EMM, keeping their original hierarchy |
| `<output>\manifest.txt` | List of collected files (UTF-8) |

Steps:

1. Resolve the paths of models, accessories, audio, background video and background image in the PMM to existing files. Search relative paths in the PMM folder, then in the MMD executable folder.
2. Follow the dependencies of each file (7.2).
3. Copy the dependencies, keeping their position relative to the original file.
4. Rewrite the PMM paths to the absolute paths of the copies. Abort if a path exceeds 255 bytes.
5. Rewrite the EMM paths (`[Object]` and effect lines) to the copies.
6. Write the PMM and EMM in the same encoding as the originals.

### 7.2 Following Dependencies

How to follow dependencies is described in chapters 2 to 4 of [Dependencies and Layout](11-dependencies.md).

### 7.3 Relinking Paths (Moving a Project)

Rewrite the absolute paths in the PMM and EMM of a project that was moved as a whole folder, so that they point to the new location. This is not a search for broken paths.

1. Determine the common folder before the move (source root): the deepest folder shared by the PMM location and every absolute path in the PMM and EMM.
2. Determine the folder after the move (destination root).
3. Convert relative EMM paths that start with `UserFile` (EMM 5.2) to absolute paths in this order.
   1. If found in a `UserFile` folder under the source root, use that absolute path and include it in the rewrite.
   2. If found only in the `UserFile` of the MMD executable folder, treat it as a shared asset and do not rewrite it.
   3. If found in neither, stop and show the list.
4. For every absolute path, replace the leading source root with the destination root. If any path does not start with the source root, stop without changing anything and show the list.
5. Write the new PMM and an EMM with the same name.

## 8. Creating a PMM from Scratch

Procedure for creating a new PMM from a given model and motion.

### 8.1 Steps

1. Build a PMM skeleton without models or accessories using the values of 8.2.
2. For each model, build the lists from the PMX (chapter 4 of [PMX Reference](06-pmx-reference.md)). Write the path as an absolute path.
3. Build each model block with the values of 8.3.
4. Import the motion VMD with the procedure of 5.3. If there is a camera VMD, import it into the camera keys.
5. Set the playback end frame and each model's last frame to at least the largest key frame.
6. Write the file with the procedures of 2.3 and 2.4.

### 8.2 Values for the Whole PMM

These values are common in real files. They are not necessarily the values of a freshly started MMD.

| Item | Value |
| --- | --- |
| Signature | `Polygon Movie maker 0002` followed by six NUL bytes |
| Output width, height | 1920, 1080 |
| Sidebar width | 430 |
| View angle | 30 |
| Editor state | All 1 |
| Initial camera key | Distance −45, position (0, 10, 0), rotation 0, parent (−1, 0), all interpolation (20, 20, 107, 107), perspective 0, view angle 30, selected 0 |
| Current camera | Same position and distance as the initial key, offset 0, rotation 0, perspective 0 |
| Initial light key and current light | Color 0.602 for each channel, direction (−0.5, −1.0, 0.5) |
| Accessories | Selected 0, scroll 0, count 0 |
| Editing and playback | Current frame 0, scroll 0, bone operation 0, follow target 0, loop and similar flags all 0, playback start 0 and end (largest frame) |
| Audio, background video, background image | None (0 and empty paths). Scale of background video and image 1.0 |
| Display | Information 1, axes 0, ground shadow 0, frame rate 60, capture 0, post-draw start position 0, ground shadow brightness 1.0, translucent ground shadow 1 |
| Physics | Mode 2 (on/off mode), acceleration 9.8, noise 10, direction (0, −1, 0), noise enabled 0 |
| Initial gravity key | Noise enabled 0, noise 10, acceleration 9.8, direction (0, −1, 0), selected 0 |
| Self shadow | Enabled 1, range 0.01125; initial key mode 1 and range 0.01125 |
| Color and follow | Edge color (0, 0, 0), black background 0, follow target (−1, 0), identity matrix, follow 0, unknown 0, ground physics 1, jump-to frame 0 |
| Extension | Present 1, (model ID, 0) for each model |

### 8.3 Values for a Model Block

As in 8.2, these values are common in real files.

| Item | Value |
| --- | --- |
| Model ID | Consecutive from 0 |
| Number of display frames | Number of PMX display frames + 1 |
| Render order, calculation order | Consecutive from 1 |
| Visible | 1 |
| Selected bone | 0 |
| Selected morphs | All −1 |
| Display frame open state | All 0 |
| Vertical scroll | 0 |
| Initial bone key | Frame 0, previous 0, next 0, all interpolation (20, 20, 107, 107), translation 0, rotation (0, 0, 0, 1), selected 0, physics off 0 |
| Initial morph key | Frame 0, weight 0, selected 0 |
| Initial display, IK and outside parent key | Visible 1, all IK 1, all outside parents (−1, 0), selected 0 |
| Current bone state | Same translation and rotation as the key at frame 0; the remaining 3 bytes are 0 |
| Current morph values | Same weight as the key at frame 0 |
| Current IK state | All 1 |
| Current outside-parent state | All (0, 0, −1, 0) |
| Rendering info | Additive blending 0, edge width 1.0, self shadow 1 |

## 9. Other Cleanup

### 9.1 Removing Redundant Keys

Removing the following keys does not change the playback result.

| Target | Keys removed |
| --- | --- |
| Bones | A key between two keys when translation, rotation and physics state are identical in all three. The first and last keys are kept |
| Bones | For a bone whose keys all have zero translation, no rotation and an unchanged physics state, every key except the initial key |
| Morphs | A key between two keys with the same weight. The first and last keys are kept |

Interpolation curves are not considered. If the values before and after are the same, the value in between does not change regardless of the curve shape.

### 9.2 Turning Physics Off

Set the physics mode (field 31 of PMM 9.1) to 0.

### 9.3 Changing the Encoding

1. Read the PMM and EMM in the original encoding (2.2).
2. Check that names and paths can be written in the new encoding. If a character cannot be written, tell the user and stop.
3. Check that bone and morph names do not exceed 19 bytes in the new encoding. Longer names will not match the PMX names when MMD loads the file.
4. Write the files in the new encoding.
