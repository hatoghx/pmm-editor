# Basics

## 1. Role

This document collects the background needed to work with PMM and EMM files. The other documents use the terms defined here.

## 2. Software and Tools

| Name | Description |
| --- | --- |
| MikuMikuDance (MMD) | Free Windows software for animating 3D characters and making videos. This material is based on version 9.32 |
| MikuMikuEffect (MME) | An add-on for MMD that changes the rendered look with shaders |

## 3. File Types

| Extension | Name | Contents | Created by |
| --- | --- | --- | --- |
| pmm | Project file | The whole scene: models and accessories used, all motion, camera, light and editor settings | MMD |
| emm | Effect assignment file | Which effect is assigned to which object | MME |
| emd | Per-model assignment file | Effect assignments of a single object | MME |
| pmx | Model | Shape, skeleton and facial expressions of a character | Model authors |
| pmd | Older model format | Same role as pmx | Model authors |
| x | Accessory | Props and backgrounds without bones (DirectX format) | Authors |
| vmd | Motion | Motion data only. There are model motions and camera motions | MMD |
| fx, fxsub | Effect | Shader programs used by MME | Authors |

A PMM does not contain the model or accessory files. It stores only their paths. Moving a PMM to another place can therefore break the references.

## 4. Scene

| Term | Meaning |
| --- | --- |
| Model | A character or stage loaded from a pmx or pmd file. It has bones and morphs |
| Accessory | An object loaded from an x file or similar. It has no bones; only position, rotation, scale and transparency can be animated |
| Bone | A joint of a model. Bones form a hierarchy and deform the model by translation and rotation |
| Morph | A part that changes the shape of a model, such as opening an eye or forming a mouth shape. Its effect is controlled by a weight from 0 to 1 |
| Material | A group of faces of a model that share the same surface settings. MME can assign an effect per material. EMM also calls it a subset |
| Display frame | A heading in the MMD editor that groups bones and morphs |
| IK | A mechanism that computes knee and thigh angles automatically from the position of the foot and similar end bones. It can be turned on and off per key |
| Outside parent | A mechanism that makes a bone follow a bone of another model, or the ground. Used for props held in a hand, for example |
| Grant (付与) | A PMX feature that passes the rotation or translation of one bone to another bone |
| Physics | Simulation that sways hair and skirts under gravity. It can be turned off per bone and per key |

## 5. Frames

| Term | Meaning |
| --- | --- |
| Frame | Unit of time. MMD uses 30 frames per second |
| Keyframe (key) | A value recorded at a point in time. Values between keys are interpolated |
| Interpolation curve | How a value changes between keys. A Bézier curve described by two control points (four numbers) |
| Initial key | The key at frame 0 that PMM always stores for every element |

## 6. Order

| Term | Meaning |
| --- | --- |
| Render order | The order in which objects are drawn. Models and accessories each have their own |
| Post-draw start position | The boundary in the accessory render order. Accessories before it are drawn before the models; the rest are drawn after the models |
| Calculation order | The order in which model motion is computed. A model that follows another model through an outside parent must be computed after that model |

## 7. Paths and Encodings

| Term | Meaning |
| --- | --- |
| UserFile | The standard folder for assets, located next to the MMD executable |
| Absolute path | A path written from the drive, such as `C:\` |
| Relative path | A path based on some folder. MMD and MME have their own rules for the base folder ([MMD Behavior](08-mmd-behavior.md)) |
| ANSI code page | The text encoding determined by the Windows language setting. It is identified by a number (table below) |

MMD and MME write strings in files in the ANSI code page of the system that saved the file. The files do not record their encoding, so the reader must determine it. Every code page that Windows uses as an ANSI code page applies. The main ones are as follows.

| Number | Language or region | Bytes per character |
| --- | --- | --- |
| 874 | Thai | 1 |
| 932 | Japanese (Shift_JIS) | 1 or 2 |
| 936 | Simplified Chinese (GBK) | 1 or 2 |
| 949 | Korean | 1 or 2 |
| 950 | Traditional Chinese (Big5) | 1 or 2 |
| 1250 | Central European | 1 |
| 1251 | Cyrillic | 1 |
| 1252 | Western European (English and others) | 1 |
| 1253 | Greek | 1 |
| 1254 | Turkish | 1 |
| 1255 | Hebrew | 1 |
| 1256 | Arabic | 1 |
| 1257 | Baltic | 1 |
| 1258 | Vietnamese | 1 |

In a 1-byte code page every byte is some character, so a wrong guess produces no broken characters. In the code pages that use 2 bytes (932, 936, 949, 950), truncating a name to 19 bytes can cut a 2-byte character in the middle.

## 8. Document List

The list of documents and when to read them is in the [Index](01-index.md).
