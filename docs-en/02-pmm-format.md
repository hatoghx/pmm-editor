# PMM File Format

## 1. Overview

PMM is the project file of MikuMikuDance. A single binary file holds the models, camera, light, accessories, keyframes and editor state. This document covers format version 0002.

Terms are defined in [Basics](01-basics.md). The relationship to PMX is described in [PMX Reference](05-pmx-reference.md) and [File Relationships](06-relations.md). How MMD opens a PMM is described in [MMD Behavior](07-mmd-behavior.md).

## 2. Notation and Common Structures

### 2.1 Types

| Notation | Other names | Meaning |
| --- | --- | --- |
| u8 | byte, uint8 | Unsigned 1-byte integer |
| i32 | int, int32 | Signed 4-byte integer (little endian) |
| f32 | float, single | IEEE 754 single-precision float (little endian) |
| vec3 | Vector3 | Three f32 values (x, y, z) |
| quat | Quaternion, Vector4 | Four f32 values (x, y, z, w) |
| bool | | u8. 0 is false, 1 is true |
| str8 | VString (variable-length string) | A length (u8) followed by that many bytes |
| fix(N) | CString (fixed-length string) | A fixed N-byte string. The value ends at the first NUL; the remaining bytes are undefined |

### 2.2 Strings

- Text is encoded in the ANSI code page of the system that saved the file. On Japanese Windows this is CP932 (Shift_JIS).
- After the NUL of a fix(N) field, leftovers of previously used strings may remain. When reading, cut at the first NUL. When writing, fill everything after the NUL with zeros.
- Paths are absolute or relative. How relative paths are resolved is described in chapter 2 of [MMD Behavior](07-mmd-behavior.md).
- Bone and morph names are the PMX names converted to the ANSI code page and truncated to 19 bytes. The cut may fall in the middle of a double-byte character. Characters that the code page cannot represent are lost.

### 2.3 Keyframe Lists

Every keyframe list has the following layout.

1. Initial records (one per element; they carry no ID)
2. Number of additional records (i32)
3. Additional records ("ID (i32) and record", repeated for the count)

| List | Number of initial records | ID of an initial record |
| --- | --- | --- |
| Bone | Number of bones | Bone index |
| Morph | Number of morphs | Morph index |
| Display, IK and outside parent | 1 | 0 |
| Camera, light, gravity, self shadow | 1 | 0 |
| Accessory (one list per accessory) | 1 | 0 |

The first 12 bytes of every record are common.

| Field | Type | Meaning |
| --- | --- | --- |
| frame | i32 | Frame number. 0 for an initial record |
| previous | i32 | ID of the previous record of the same element |
| next | i32 | ID of the next record of the same element. 0 for the last record |

The chain follows these rules.

- Following next from the initial record visits the keys of that element in ascending frame order.
- The previous of the first additional record is the ID of the initial record.
- IDs of additional records appear in ascending order in the file and are not smaller than the number of initial records. Gaps are allowed.
- The previous of an initial record is a value MMD does not use. Some files hold a non-zero value in the camera (14 files) and light (4 files) lists. The value does not match the last ID of the chain. Keeping the original value when reading and writing is enough.

### 2.4 Interpolation Curves

Each interpolation curve is 4 bytes (x1, y1, x2, y2) with values from 0 to 127. The 4 bytes of one curve are stored together.

| Target | Curve order | Size |
| --- | --- | --- |
| Bone | X move, Y move, Z move, rotation | 16 bytes |
| Camera | X move, Y move, Z move, rotation, distance, view angle | 24 bytes |

## 3. Overall Layout

| Order | Section | Chapter |
| --- | --- | --- |
| 1 | Header | 4 |
| 2 | Models | 5 |
| 3 | Camera | 6 |
| 4 | Light | 7 |
| 5 | Accessories | 8 |
| 6 | Editing, playback, background and rendering settings | 9 |
| 7 | Gravity keys | 10 |
| 8 | Self shadow | 11 |
| 9 | Color and follow settings | 12 |
| 10 | Extension | 13 |

There are no separators or length fields between sections. Read them in order by counting elements.

## 4. Header

| Field | Type | Meaning |
| --- | --- | --- |
| Signature | 30 bytes | The first 24 bytes are `Polygon Movie maker 0002`, followed by NUL. The last 5 bytes are undefined |
| Output width | i32 | |
| Output height | i32 | |
| Sidebar width | i32 | Width of the right-hand panel of the editor |
| View angle | f32 | View angle of the current camera (degrees) |
| Editor state | bool × 7 | See the table below |
| Selected model | u8 | ID of the selected model |
| Model count | u8 | |

Identify the file by the first 24 bytes and the NUL that follows.

The 7 editor state bytes are, in order:

| Order | Meaning |
| --- | --- |
| 1 | The editor is in camera, light and accessory mode |
| 2 | Camera panel is open |
| 3 | Light panel |
| 4 | Accessory panel |
| 5 | Bone panel |
| 6 | Facial (morph) panel |
| 7 | Self shadow panel |

## 5. Models

### 5.1 Layout

The following block is repeated for each model.

| Order | Field | Type | Meaning |
| --- | --- | --- | --- |
| 1 | Model ID | u8 | Number used by other sections to refer to the model. See 5.8 |
| 2 | Model name | str8 | |
| 3 | Model name (English) | str8 | |
| 4 | Model path | fix(256) | |
| 5 | Number of display frames | u8 | See 5.8 |
| 6 | Bone count | i32 | |
| 7 | Bone names | str8 × bone count | |
| 8 | Morph count | i32 | |
| 9 | Morph names | str8 × morph count | |
| 10 | IK count | i32 | |
| 11 | IK bone indices | i32 × IK count | |
| 12 | Number of outside-parent slots | i32 | Includes the leading empty slot |
| 13 | Indices of bones that can have an outside parent | i32 × slots | The first value is always −1. Which bones appear is described in chapter 4 of PMX Reference |
| 14 | Render order | u8 | Starts at 1 |
| 15 | Visible | bool | |
| 16 | Selected bone | i32 | Index. −1 if none |
| 17 | Selected morphs | i32 × 4 | Eyebrow, eye, mouth, other. −1 if none |
| 18 | Number of display frames | u8 | Same value as field 5 |
| 19 | Display frame open state | bool × display frames | |
| 20 | Vertical scroll of the key panel | i32 | |
| 21 | Last frame | i32 | Not smaller than the largest key frame |
| 22 | Bone keyframe list | Layout of 2.3 | See 5.2 |
| 23 | Morph keyframe list | Layout of 2.3 | See 5.3 |
| 24 | Display, IK and outside parent keyframe list | Layout of 2.3 | See 5.4 |
| 25 | Current bone state | 31 bytes × bone count | See 5.5 |
| 26 | Current morph values | f32 × morph count | Weights |
| 27 | Current IK state | bool × IK count | |
| 28 | Current outside-parent state | 16 bytes × slots | See 5.6 |
| 29 | Rendering info | 7 bytes | See 5.7 |

### 5.2 Bone Key (58 bytes)

| Field | Type | Meaning |
| --- | --- | --- |
| frame, previous, next | i32 × 3 | See 2.3 |
| Interpolation curves | 16 bytes | See 2.4 |
| Translation | vec3 | |
| Rotation | quat | |
| Selected | bool | Selection state in the key panel |
| Physics off | bool | 1 turns physics off at this key |

### 5.3 Morph Key (17 bytes)

| Field | Type | Meaning |
| --- | --- | --- |
| frame, previous, next | i32 × 3 | See 2.3 |
| Weight | f32 | |
| Selected | bool | |

### 5.4 Display, IK and Outside Parent Key

With a as the IK count and b as the number of outside-parent slots, the record is 13 + a + 8b + 1 bytes.

| Field | Type | Meaning |
| --- | --- | --- |
| frame, previous, next | i32 × 3 | See 2.3 |
| Visible | bool | |
| IK enabled | bool × a | In the order of field 11 of 5.1 |
| Outside parent | (i32, i32) × b | Parent model ID and bone index. (−1, 0) if none |
| Selected | bool | |

Outside-parent values follow these rules.

| Parent model ID | Parent bone index | Meaning |
| --- | --- | --- |
| −2 | 0 | Ground |
| −1 | 0 | None |
| 0 or more | Index of an existing bone | Follows that bone of that model. MMD rejects a combination that does not point to a bone |

### 5.5 Current Bone State (31 bytes)

| Field | Type | Meaning |
| --- | --- | --- |
| Translation | vec3 | |
| Rotation | quat | |
| Physics off | bool | 1 turns physics off for the current pose |
| Unregistered change | bool | 1 means the bone was moved by hand and not yet registered as a key |
| Selected | bool | |

### 5.6 Current Outside-Parent State (16 bytes)

| Field | Type | Meaning |
| --- | --- | --- |
| Start frame | i32 | |
| End frame | i32 | |
| Parent model ID | i32 | −1 if none |
| Parent bone index | i32 | |

The parent model ID may point to a model that is not in the file. A reader must not stop when the target is missing.

### 5.7 Rendering Info (7 bytes)

| Field | Type | Meaning |
| --- | --- | --- |
| Additive blending | bool | |
| Edge width | f32 | |
| Self shadow | bool | |
| Calculation order | u8 | Starts at 1 |

### 5.8 Model ID and Number of Display Frames

The model ID is the number that identifies a model inside the PMM. It is referenced from:

- The selected model in the header
- Outside parents (5.4 and 5.6)
- The camera parent (6.2) and the camera follow target (chapter 12)
- Accessory parents (8.3 and 8.4)
- The extension (chapter 13)

In files saved by MMD, IDs do not always run from 0 in order. A writer may renumber them from 0. In that case every reference above must be rewritten with the same mapping.

For a model that has morphs, the number of display frames is the number of PMX display frames plus 1 (the facial frame is added). For a model without morphs, it equals the number of PMX display frames. Of 1,162 entries examined, 1,149 follow this rule; the other 13 (with morphs) had the same number as the PMX. When this number does not match the loaded model, MMD ignores the open state of the display frames and continues. A mismatch therefore does not make loading fail.

## 6. Camera

### 6.1 Layout

1. Camera keyframe list (layout of 2.3, records in 6.2)
2. Current camera (6.3)

### 6.2 Camera Key (78 bytes)

| Field | Type | Meaning |
| --- | --- | --- |
| frame, previous, next | i32 × 3 | See 2.3 |
| Distance | f32 | Distance from the target point to the eye. Usually negative |
| Position | vec3 | Target point |
| Rotation | vec3 | Radians |
| Parent model ID | i32 | −1 if none |
| Parent bone index | i32 | Ignore when the parent model ID is −1 |
| Interpolation curves | 24 bytes | See 2.4 |
| Perspective | bool | 0 is perspective projection, 1 is orthographic projection |
| View angle | i32 | Degrees |
| Selected | bool | |

### 6.3 Current Camera

| Field | Type | Meaning |
| --- | --- | --- |
| Position | vec3 | Target point |
| Eye offset | f32 × 2 | X and Y offset from the target point |
| Distance | f32 | |
| Rotation | vec3 | Radians |
| Perspective | bool | Same as in 6.2 |

## 7. Light

### 7.1 Layout

1. Light keyframe list (layout of 2.3, records in 7.2)
2. Current light (color vec3, direction vec3)

### 7.2 Light Key (37 bytes)

| Field | Type | Meaning |
| --- | --- | --- |
| frame, previous, next | i32 × 3 | See 2.3 |
| Color | vec3 | Red, green, blue (0 to 1) |
| Direction | vec3 | Direction of the light |
| Selected | bool | |

## 8. Accessories

### 8.1 Layout

| Order | Field | Type | Meaning |
| --- | --- | --- | --- |
| 1 | Selected accessory | u8 | Accessory ID |
| 2 | Vertical scroll of the list | i32 | |
| 3 | Accessory count | u8 | |
| 4 | Name list | fix(100) × accessory count | |
| 5 | Accessory blocks | 8.2 × accessory count | |

### 8.2 Accessory Block

| Order | Field | Type | Meaning |
| --- | --- | --- | --- |
| 1 | Accessory ID | u8 | |
| 2 | Name | fix(100) | |
| 3 | Path | fix(256) | |
| 4 | Render order | u8 | Starts at 0 |
| 5 | Keyframe list | Layout of 2.3 | Records in 8.3 |
| 6 | Current state | 39 bytes | See 8.4 |

### 8.3 Accessory Key (51 bytes)

| Field | Type | Meaning |
| --- | --- | --- |
| frame, previous, next | i32 × 3 | See 2.3 |
| Visibility and transparency | u8 | See 8.5 |
| Parent model ID | i32 | −1 if none |
| Parent bone index | i32 | |
| Position | vec3 | |
| Rotation | vec3 | Radians |
| Scale | f32 | |
| Shadow | bool | |
| Selected | bool | |

### 8.4 Current Accessory State (39 bytes)

| Field | Type | Meaning |
| --- | --- | --- |
| Visibility and transparency | u8 | See 8.5 |
| Parent model ID | i32 | |
| Parent bone index | i32 | |
| Rotation | vec3 | |
| Scale | f32 | |
| Position | vec3 | |
| Shadow | bool | |
| Additive blending | bool | |

The order differs from the key. The key stores position, rotation, scale; the current state stores rotation, scale, position.

### 8.5 Visibility and Transparency Byte

| Bits | Meaning |
| --- | --- |
| 0 | Visible (1 means visible) |
| 1 to 7 | Transparency (percentage from 0 to 100) |

Opacity is (100 − transparency) ÷ 100.

## 9. Editing, Playback, Background and Rendering Settings

### 9.1 Layout

| Order | Field | Type | Meaning |
| --- | --- | --- | --- |
| 1 | Current frame | i32 | |
| 2 | Horizontal scroll position of the key panel | i32 | |
| 3 | Horizontal scroll length of the key panel | i32 | |
| 4 | Bone operation mode | i32 | See 9.2 |
| 5 | Camera follow target | u8 | 0 none, 1 model, 2 bone |
| 6 | Loop playback | bool | |
| 7 | Move current frame on stop | bool | |
| 8 | Play from current frame | bool | |
| 9 | Playback start frame | i32 | |
| 10 | Playback end frame | i32 | |
| 11 | Audio enabled | bool | |
| 12 | Audio path | fix(256) | |
| 13 | Background video X offset | i32 | |
| 14 | Background video Y offset | i32 | |
| 15 | Background video scale | f32 | |
| 16 | Background video path | fix(256) | |
| 17 | Background video state | i32 | See 9.3 |
| 18 | Background image X offset | i32 | |
| 19 | Background image Y offset | i32 | |
| 20 | Background image scale | f32 | |
| 21 | Background image path | fix(256) | |
| 22 | Show background image | bool | |
| 23 | Show information | bool | |
| 24 | Show axes | bool | |
| 25 | Ground shadow | bool | |
| 26 | Frame rate limit | f32 | 30, 60 or 1000 (unlimited) |
| 27 | Screen capture mode | i32 | See 9.4 |
| 28 | Post-draw start position | i32 | Accessories with a render order smaller than this value are drawn before the models |
| 29 | Ground shadow brightness | f32 | |
| 30 | Translucent ground shadow | bool | |
| 31 | Physics mode | u8 | See 9.5 |
| 32 | Gravity acceleration | f32 | |
| 33 | Gravity noise amount | i32 | |
| 34 | Gravity direction | vec3 | |
| 35 | Gravity noise enabled | bool | |

The background video and background image fields are present even when the path is empty.

### 9.2 Bone Operation Mode

| Value | Meaning |
| --- | --- |
| 0 | Select |
| 1 | Rectangle select |
| 2 | No button pressed |
| 3 | Rotate |
| 4 | Move |

### 9.3 Background Video State

| Value | Meaning |
| --- | --- |
| 0 | None |
| 1 | Background video shown |
| 64, 65 | Background video is not used. They appear when the path is empty (loading enables the video only when the value is 1) |

### 9.4 Screen Capture Mode

| Value | Meaning |
| --- | --- |
| 0 | None |
| 1 | Full screen |
| 2 | Square |
| 3 | Background video |

### 9.5 Physics Mode

| Value | Meaning |
| --- | --- |
| 0 | Disabled |
| 1 | Always on |
| 2 | On/off mode |
| 3 | Trace |

## 10. Gravity Keys

The gravity keyframe list uses the layout of 2.3 with the following 34-byte records.

| Field | Type | Meaning |
| --- | --- | --- |
| frame, previous, next | i32 × 3 | See 2.3 |
| Noise enabled | bool | |
| Noise amount | i32 | |
| Acceleration | f32 | |
| Direction | vec3 | |
| Selected | bool | |

## 11. Self Shadow

### 11.1 Layout

| Order | Field | Type | Meaning |
| --- | --- | --- | --- |
| 1 | Self shadow enabled | bool | |
| 2 | Range | f32 | See 11.3 |
| 3 | Keyframe list | Layout of 2.3 | Records in 11.2 |

### 11.2 Self Shadow Key (18 bytes)

| Field | Type | Meaning |
| --- | --- | --- |
| frame, previous, next | i32 × 3 | See 2.3 |
| Mode | u8 | 0 none, 1 mode 1, 2 mode 2 |
| Range | f32 | See 11.3 |
| Selected | bool | |

### 11.3 Range Value

The value in the file uses a different scale from the value shown in the editor. The editor default of 8875 is stored as 0.01125. The conversion is "editor value = 10000 − file value × 100000" (rounded).

## 12. Color and Follow Settings

| Order | Field | Type | Meaning |
| --- | --- | --- | --- |
| 1 | Edge color | i32 × 3 | Red, green, blue |
| 2 | Black background | bool | |
| 3 | Camera follow model | i32 | −1 if none |
| 4 | Camera follow bone | i32 | |
| 5 | Follow orientation | f32 × 16 | 4 × 4 matrix. Identity when there is no follow target |
| 6 | Camera follow enabled | bool | |
| 7 | Camera follow suppression | u8 | Only read and written; MMD does not use it. 0 in all 134 files |
| 8 | Ground physics | bool | |
| 9 | Jump-to frame | i32 | |

## 13. Extension

This part was added in MikuMikuDance 9.24. Older files end after chapter 12.

| Field | Type | Meaning |
| --- | --- | --- |
| Range selection present | bool | The next field exists only when this is 1 |
| Range selection state | (u8, i32) × model count | Model ID and range selection target |

Range selection target values:

| Value | Target |
| --- | --- |
| 0 | All frames |
| 1 | First bone |
| 2 | Display, IK and outside parent |
| 3 | Selected bones |
| 4 | Selected morphs |
| 5 | All morph frames |
| 6 | All bone frames (only for models without morphs) |
| 7 and above | Numbers assigned to morphs, then bones |

## Appendix A. Differences in Version 0001

Version 0001 is written by old versions of MikuMikuDance. Only the trailing digits of the signature are `0001`. The three sample PMMs shipped with MMD 9.32 are read to the last byte with the structure below.

### A.1 Header

| Item | Difference from 0002 |
| --- | --- |
| Editor state | 6 bytes (0002 has 7). They correspond to the 2nd to 7th bytes of 0002 |
| Right after the model count | One 20-byte name per model |

### A.2 Model

| Order | Item | Type |
| --- | --- | --- |
| 1 | Model ID | u8 |
| 2 | Model name | fix(20) |
| 3 | Model path | fix(256) |
| 4 | Render order | u8 |
| 5 | Visible | bool |
| 6 | Selected bone | i32 |
| 7 | Selected morphs | i32 × 4 |
| 8 | Display frame count and open states | u8 followed by that many bool |
| 9 | Key area vertical scroll, last frame | i32 × 2 |
| 10 | Bone key list | Initial records are 57 bytes × bone count; added records are a count (i32) followed by 61 bytes (ID and 57 bytes) × count |
| 11 | Morph key list | Initial records are 17 bytes × morph count; added records are a count followed by 21 bytes × count |
| 12 | Display/IK key list | Initial record is 13 + IK count + 1 bytes; added records are preceded by an ID (i32) |
| 13 | Current bone state | 34 bytes × bone count (vec3, quat, bool × 2) |
| 14 | Current morph values, current IK state | f32 × morph count, bool × IK count |

The bone count, morph count (including the base morph) and IK count are counted from the model file (PMD). There are no outside-parent slots.

### A.3 Other Differences

| Section | Difference from 0002 |
| --- | --- |
| Camera key | No parent model ID or parent bone index (70 bytes) |
| Gravity keys | Not present |
| Accessory current state | Ends with a 1-byte bool (0002 ends with two bool bytes) |
| Edit, playback, background and drawing settings | Same as 0002 up to the capture mode |

### A.4 Conditions at the End

After the capture mode, each block ends with a 1-byte marker (1 means the next block follows). When the marker is not 1, the file ends there. The blocks are in this order.

| Order | Content |
| --- | --- |
| 1 | Post-draw start position, ground shadow brightness, additive blend per model, additive blend per accessory |
| 2 | Ground shadow transparency, marker |
| 3 | Edge thickness per model (f32), marker |
| 4 | Physics mode, gravity (acceleration, noise amount, direction, noise enabled), marker |
| 5 | Self-shadow mode, range, per-model setting, marker |
| 6 | Self-shadow key list, marker |
| 7 | Edge color (i32 × 3), marker |
| 8 | Black background, marker |
| 9 | Camera parent (model ID, bone index), camera parent list, marker. This block is always present |
| 10 | Follow orientation matrix, camera follow, follow suppression, marker |
| 11 | Ground physics, marker |
| 12 | Calculation order per model |

Among the samples, one ends after block 4, one after block 11, and one after block 9.
