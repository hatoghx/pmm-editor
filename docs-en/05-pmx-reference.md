# PMX Reference

## 1. Overview

A PMM keeps copies of a model's bone names, morph names, IK list and list of bones that can have an outside parent, all taken from the PMX file. This document records only the parts of PMX needed to build those lists. It does not cover the whole PMX format.

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
