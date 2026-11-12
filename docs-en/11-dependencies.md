# Dependencies and Layout

## 1. Overview

This document describes how to follow every file used by a PMM, and every file those files refer to, collect them, and arrange them in the layout the user wants. Chapters 2 to 4 cover how to follow references, and chapter 5 covers how to arrange the files. The overall procedure is in chapter 7 of [Procedures](09-operations.md).

## 2. Overview of References

| Referrer | Target | Where it is written |
| --- | --- | --- |
| PMM | Models (pmx, pmd), accessories (x, vac), audio, background video, background image | The paths in the PMM (PMM 5.1, 8.2, 9.1) |
| PMM | EMM | Same name (Relations 2.1) |
| EMM | Effects (fx, fxsub and others) | `[Object]` and the effect lines (EMM chapters 5 and 7) |
| Model | EMD with the same name | The model file name with the extension replaced by emd (3.5) |
| EMD | Effects | The `Obj` and `Obj[n]` lines (3.5) |
| pmx | Textures, sphere maps, toons | The texture table and materials (3.1) |
| pmd | Textures, sphere maps, toons | Materials and the toon name table (3.2) |
| x | Textures | `TextureFilename` (3.3) |
| vac | x | Line 2 (3.4) |
| fx, fxsub | fx, fxsub, textures | `#include` and file names in quotes (3.6) |

A PMM holds no VMD paths. The motion is inside the PMM, so it is not an object to collect. To hand it over as VMD, extract it with 5.1 and 5.2 of [Procedures](09-operations.md).

## 3. How to Read References

### 3.1 pmx

PMX has versions 2.0 and 2.1; the part used here has the same layout. Read from the start in this order.

| Order | Item | Content |
| --- | --- | --- |
| 1 | Signature | `PMX ` (4 bytes), version (f32) |
| 2 | Globals | A count (u8, normally 8) followed by that many u8 values. In order: encoding (0 is UTF-16 little endian, 1 is UTF-8), number of additional UVs, vertex index size, texture index size, material index size, bone index size, morph index size, rigid body index size |
| 3 | Model info | Four strings (model name, English name, comment, English comment) |
| 4 | Vertices | Count (i32) and a record per vertex. The record size is given below |
| 5 | Faces | Index count (i32) and vertex index size × count |
| 6 | Texture table | Count (i32) and strings × count. Paths are based on the folder of the pmx |
| 7 | Materials | Count (i32) and a record per material (table below) |

A string is a length (i32) followed by that many bytes.

The size of a vertex record is the sum of:

- Position, normal, UV: 12 + 12 + 8 bytes
- Additional UVs: 16 bytes × number of additional UVs
- Weight type (u8) and its content (let n be the bone index size)
  - 0: n
  - 1: 2n + 4
  - 2: 4n + 16
  - 3: 2n + 4 + 36
  - 4: 4n + 16
- Edge scale: 4 bytes

The parts of a material record needed to read references are:

| Order | Item | Type |
| --- | --- | --- |
| 1 | Material name, English name | String × 2 |
| 2 | Diffuse, specular, specular strength, ambient | 16 + 12 + 4 + 12 bytes |
| 3 | Drawing flags | u8 |
| 4 | Edge color, edge size | 16 + 4 bytes |
| 5 | Texture index | Texture index size (signed; −1 means none) |
| 6 | Sphere index | Same |
| 7 | Sphere mode | u8 |
| 8 | Shared toon flag | u8 |
| 9 | Toon index | Texture index size when the flag is 0; u8 when the flag is 1 |
| 10 | Memo | String |
| 11 | Face count | i32 |

When the shared toon flag is 1, toon indices 0 to 9 point to `toon01.bmp` to `toon10.bmp`, which MMD provides in the `Data` folder of the MMD executable folder. Do not collect them.

In the 1,088 pmx files at hand, every file could be read to the last material in this order, and every texture index fell inside the texture table. Of the 8,217 entries in the texture tables, 8,110 existed relative to the pmx folder.

### 3.2 pmd

Read from the start in this order.

| Order | Item | Size |
| --- | --- | --- |
| 1 | Signature, version, model name, comment | 3 + 4 + 20 + 256 bytes |
| 2 | Vertices | Count (u32) and 38 bytes × count |
| 3 | Faces | Index count (u32) and 2 bytes × count |
| 4 | Materials | Count (u32) and 70 bytes × count |
| 5 | Bones | Count (u16) and 39 bytes × count |
| 6 | IK | Count (u16) and, per IK, 11 bytes + 2 bytes × number of influenced bones |
| 7 | Morphs | Count (u16) and, per morph, 20 + 4 + 1 bytes + 16 bytes × number of vertices |
| 8 | Morph display | Count (u8) and 2 bytes × count |
| 9 | Bone group names | Count (u8) and 50 bytes × count |
| 10 | Bone group bones | Count (u32) and 3 bytes × count |
| 11 | English names present | u8. When 1: model name 20, comment 256, bone names 20 × bone count, morph names 20 × (morph count − 1), bone group names 50 × group count |
| 12 | Toon name table | 100 bytes × 10 |

Of the 70 bytes of a material, two parts are needed to read references.

| Position (from the start, counting from 0) | Item | Content |
| --- | --- | --- |
| 44 | Toon number (u8) | 0 to 9 is a number in the toon name table. 255 means none |
| 50 | Texture name (20 bytes) | The value ends at the first NUL. When separated by `*`, the first part is the texture and the second is the sphere map (`.sph` or `.spa`) |

In the toon name table, `toon01.bmp` to `toon10.bmp` are the defaults, and MMD provides them. A name other than the defaults (for example `toon_hada.bmp`) is collected as a file in the model folder. In all 22 pmd files at hand, the toon name table could be read in this order, and 14 entries had names other than the defaults.

Texture paths are based on the folder of the pmd.

### 3.3 x

A text-format x file, which starts with `xof 0302txt`, refers to a texture in the form `TextureFilename { "file name"; }`. The path is based on the folder of the x file. A binary-format x file, which starts with `xof 0302bin`, cannot be followed.

### 3.4 vac

A vac file is a text file holding accessory settings, with these 6 lines.

| Line | Content |
| --- | --- |
| 1 | Accessory name |
| 2 | File name of the x |
| 3 | Scale |
| 4 | Position (x, y, z) |
| 5 | Rotation angles (x, y, z) |
| 6 | Bone name (the bone whose motion the accessory follows) |

The x on line 2 is an object to collect. Its path is based on the folder of the vac.

### 3.5 EMD

If an EMD with the same name as the model file, differing only in extension, is in the same folder, MME applies it automatically on load (EMM Appendix A). An EMD consists of `[Info]` and `[Effect]`, and the values of `Obj` and `Obj[n]` in `[Effect]` are effect paths (the form of EMM chapter 7). Collect these effects.

### 3.6 fx and fxsub

Read the file as text and treat the following two kinds as references.

- The target of `#include`
- File names with an extension inside quotes (textures, fxsub and so on)

When the target is an fx or fxsub, follow it further.

## 4. Procedure for Following References

1. Put the root files (the models, accessories, audio and background of the PMM, and the effects of the EMM) in a work list. Resolve the PMM paths to existing files (MMD Behavior 2.2).
2. Take one unprocessed file from the list and read its references according to chapter 3 for its kind.
3. Search for each referenced path in this order: the folder of the referring file, the folder of the root file, and the folder common to both. If it is not found, record its kind (texture and so on) and name as not found.
4. Of the files found, add to the list those not yet in it, except kinds that are not collected (the shared toons that MMD provides and `screen.bmp`). Do not follow the same file twice.
5. Repeat steps 2 to 4 until the list is empty.
6. Tell the user the list of collected files and the list of files not found.

## 5. Layout Methods

### 5.1 Common Conditions

- Paths of references are written relative to the referrer. Keep the relative position between a referrer and its dependencies after copying. Otherwise textures of a pmx and `#include` of an fx are no longer found.
- Therefore the files of one model (pmx, pmd, textures, sphere maps, toons, EMD) are copied under a single folder with their original hierarchy, without splitting them by kind.
- When the relative position cannot be kept, stop and tell the user. Example: a dependency is on another drive.
- When several models use the same texture, copy it once per model folder so that each model folder is self-contained. Copy an effect only once, because it is referenced from the EMM.
- When folder or file names collide, tell them apart with a number (example: `Model_2`).
- The absolute path written into the PMM must be 255 bytes or less and representable in the code page of the PMM (Validation chapters 4 and 5).

### 5.2 Methods

| Method | Layout | Suited to |
| --- | --- | --- |
| Keep the original hierarchy | Take the common folder (decided as the source root of 7.3) as the root and copy each file to its original relative position | Putting the files back later, or handing them over in the original structure |
| By project | Under `<output>\<project name>\`, copy the PMM, EMM, models, accessories, effects and media. The structure is the example in 7.1 of [Procedures](09-operations.md) | Handing over a project as one folder |
| By asset | Create a folder per kind, with names chosen by the user. Example: models in `<model folder name>\<model name>\`, accessories in `<accessory folder name>\`, effects in `<effect folder name>\`, motion in `<animation folder name>\`. Inside a model folder, keep the original hierarchy as in 5.1 | Organizing assets by kind |
| By model | Create a folder per model and put in it the model, its dependencies, the EMD of the model and the motion (VMD) of the model | Handing over a model together with its motion |

In every method, rewrite the paths inside the PMM and EMM to the paths of the copies. In a method that includes motion (VMD), extract the VMD from the PMM (Procedures 5.1 and 5.2) and write it to the folder the user specified.

### 5.3 What to Confirm with the User

| Item | Example |
| --- | --- |
| Method | Keep the original hierarchy, by project, by asset, by model, or something else |
| Output location and names of folders and files | Folder names per kind, project name |
| Whether to hand over motion | If so, per model or combined, whether to include the camera, the encoding of the VMD |
| Scope to collect | Whether to include the EMM and effects, EMD and media |
| Handling of items not found | Report and continue, or stop |

When the user's request does not fit a method, arrange the files as the user specifies, within the conditions of 5.1.

### 5.4 Checks After Collecting

1. Confirm that every path in the written PMM and EMM exists (Validation chapter 5).
2. Confirm again with the procedure of chapter 4 that the references of the pmx, pmd, x and fx files are found in the copies.
3. Confirm that every file in the list was copied.
