# VMD File Format

## 1. Overview

VMD is a file that stores motion only. It is used when extracting motion from a PMM and when importing motion into a PMM. This document covers what those tasks need.

There are two kinds of VMD.

| Kind | Contents | How to tell |
| --- | --- | --- |
| Model motion | Bone, morph, and display and IK keys | The model name in the header is the name of the target model |
| Camera motion | Camera, light and self shadow keys | The model name in the header is `カメラ・照明` |

Types are written as in 2.1 of [PMM File Format](03-pmm-format.md). u32 is an unsigned 4-byte integer.

## 2. Overall Layout

| Order | Field | Type | Meaning |
| --- | --- | --- | --- |
| 1 | Signature | 30 bytes | `Vocaloid Motion Data 0002` followed by NUL. The rest is zero |
| 2 | Model name | fix(20) | |
| 3 | Bone keys | u32 count + 111 bytes × count | Chapter 3 |
| 4 | Morph keys | u32 count + 23 bytes × count | Chapter 4 |
| 5 | Camera keys | u32 count + 61 bytes × count | Chapter 5 |
| 6 | Light keys | u32 count + 28 bytes × count | Chapter 6 |
| 7 | Self shadow keys | u32 count + 9 bytes × count | Chapter 7 |
| 8 | Display and IK keys | u32 count + variable × count | Chapter 8 |

- Older VMD files end after an earlier section. When the reader reaches the end of the file, treat the remaining sections as having 0 entries.
- Keys may appear in any order.
- Strings use the ANSI code page of the environment where the VMD is used. A VMD for a Japanese MMD is 932.
- Unlike PMM, there is no key chain (previous, next). Keys of the same bone are distinguished only by name and frame number.

## 3. Bone Key (111 bytes)

| Field | Type | Meaning |
| --- | --- | --- |
| Bone name | fix(15) | |
| Frame number | u32 | |
| Translation | vec3 | |
| Rotation | quat | x, y, z, w |
| Interpolation curves | 64 bytes | See 3.1 |

Bone names are limited to 15 bytes. PMM bone names can be up to 19 bytes, so names of 16 bytes or more do not fit into a VMD (chapter 4 of [File Relationships](07-relations.md)).

### 3.1 Interpolation Layout

The control points of the four curves (X move, Y move, Z move, rotation) are interleaved byte by byte. Writing the control points of curve c as c.x1, c.y1, c.x2, c.y2, the first 16 bytes are:

| Position | 0 | 1 | 2 | 3 | 4 | 5 | 6 | 7 |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Value | X.x1 | Y.x1 | Z.x1 | R.x1 | X.y1 | Y.y1 | Z.y1 | R.y1 |

| Position | 8 | 9 | 10 | 11 | 12 | 13 | 14 | 15 |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Value | X.x2 | Y.x2 | Z.x2 | R.x2 | X.y2 | Y.y2 | Z.y2 | R.y2 |

The remaining 48 bytes repeat the first 16 bytes three times, each shifted by one byte. The second row is the 15 bytes starting at the second byte followed by one zero, the third row is the 14 bytes starting at the third byte followed by two zeros, and the fourth row is the 13 bytes starting at the fourth byte followed by three zeros.

VMD files written by other software may leave the repeated part empty. When reading, use the following positions, which hold the same values in every variant.

| Curve | x1 | y1 | x2 | y2 |
| --- | --- | --- | --- | --- |
| X move | 0 | 4 | 8 | 12 |
| Y move | 16 | 20 | 24 | 28 |
| Z move | 32 | 36 | 40 | 44 |
| Rotation | 48 | 52 | 56 | 60 |

PMM bone keys store the 4 bytes of each curve together. Converting between PMM and VMD requires reordering.

## 4. Morph Key (23 bytes)

| Field | Type | Meaning |
| --- | --- | --- |
| Morph name | fix(15) | |
| Frame number | u32 | |
| Weight | f32 | |

## 5. Camera Key (61 bytes)

| Field | Type | Meaning |
| --- | --- | --- |
| Frame number | u32 | |
| Distance | f32 | |
| Position | vec3 | Target point |
| Rotation | vec3 | Radians |
| Interpolation curves | 24 bytes | See 5.1 |
| View angle | u32 | Degrees |
| Perspective | u8 | Same value as the perspective byte of a PMM camera key |

### 5.1 Interpolation Layout

The six curves (X move, Y move, Z move, rotation, distance, view angle) are stored in this order, 4 bytes each. The order inside one curve differs from PMM.

| Format | Order inside one curve |
| --- | --- |
| VMD | x1, x2, y1, y2 |
| PMM | x1, y1, x2, y2 |

## 6. Light Key (28 bytes)

| Field | Type | Meaning |
| --- | --- | --- |
| Frame number | u32 | |
| Color | vec3 | Red, green, blue (0 to 1) |
| Direction | vec3 | |

## 7. Self Shadow Key (9 bytes)

| Field | Type | Meaning |
| --- | --- | --- |
| Frame number | u32 | |
| Mode | u8 | 0 none, 1 mode 1, 2 mode 2 |
| Range | f32 | Same scale as the PMM range |

## 8. Display and IK Key

| Field | Type | Meaning |
| --- | --- | --- |
| Frame number | u32 | |
| Visible | u8 | |
| IK count | u32 | |
| IK state | (fix(20), u8) × IK count | IK bone name and 1 if enabled |

Outside parents stored in the PMM display, IK and outside parent keys are not included in VMD.
