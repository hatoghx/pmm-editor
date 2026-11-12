# PMX Reference

## 1. Overview

A PMM keeps copies of a model's bone names, morph names, IK list and list of bones that can have an outside parent, all taken from the PMX file. This document records only the parts of PMX needed to build those lists. It does not cover the whole PMX format.For packaging, it also records, in chapter 5, how to read the textures that materials refer to.

## 2. Items Read from PMX

| PMX item | Information used |
| --- | --- |
| Model name, model name (English) | Model names in the PMM |
| Bones | Name and flags (chapter 3) |
| Morphs | Name |
| Display frames | Count |

PMX strings are UTF-16 or UTF-8 (set in the header). Convert them to the ANSI code page when copying them into a PMM.

## 3. Bone Flags

Each PMX bone has 2 bytes of flags. Building the PMM lists uses the following bits.

| Bit | Value | Meaning |
| --- | --- | --- |
| 2 | 0x0004 | Translatable |
| 3 | 0x0008 | Visible |
| 4 | 0x0010 | Operable |
| 5 | 0x0020 | IK |

## 4. Building the PMM Lists

| PMM field (PMM 5.1) | How to build it from PMX |
| --- | --- |
| Bone names (field 7) | All PMX bones, in file order |
| Morph names (field 9) | All PMX morphs, in file order |
| IK bone indices (field 11) | Indices of bones with the IK bit (0x0020), ascending |
| Indices of bones that can have an outside parent (field 13) | −1 first, then the indices of bones matching either condition below, ascending |
| Number of display frames (fields 5 and 18) | Number of PMX display frames plus 1 (PMM 5.8) |

A bone can have an outside parent when either of the following is true.

1. It has the IK bit (0x0020).
2. It has all three bits: translatable (0x0004), visible (0x0008) and operable (0x0010).

Names are converted to the ANSI code page and truncated to 19 bytes (PMM 2.2).

## 5. Reading the Files Referenced by Materials

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
