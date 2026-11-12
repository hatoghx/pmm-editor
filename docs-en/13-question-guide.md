# Question Guide

## 1. Overview

This table maps questions and requests to the sections to use. Short section names such as "Procedures 3.1" follow chapter 3 of the [Index](01-index.md).

## 2. Rules for Every Request

1. Do not overwrite the original PMM and EMM; write the result under a different name (Procedures 2.1).
2. Determine the encoding before reading (Procedures 2.2).
3. If an EMM with the same name exists, read it together with the PMM and write them together (Relations 2.1).
4. When a model or bone name does not exactly match the words of the request, show the candidates and confirm. Model names often end with a version or costume name.
5. After writing, read the file again and confirm that it reads to the last byte (Procedures 2.1).
6. Report what was changed with model names, bone names and counts.

## 3. Questions and Sections

### 3.1 Models and Accessories

| Example question | Confirm | Sections |
| --- | --- | --- |
| I want to duplicate a model | Target model, where to place it, whether to copy effects | Procedures 3.1, 3.2 |
| I want to duplicate an accessory | Target accessory | Procedures 3.3 |
| I want to delete a model or accessory | Target, whether other models use it as an outside parent | Procedures 3.4 |
| I want to change the render order | The new order | Procedures 3.5 |
| I want to bring a model from another PMM | Source and destination, whether both have an EMM | Procedures 3.6 |
| I want to replace a model with another model that has the same bones | Path of the new model | Relations 3.3, field 4 of PMM 5.1, Relations 2.8 |
| I want a list of the models and bones in a PMM | | PMM 5.1 |
| What are the limits on models and accessories? | | Procedures 2.6 |

### 3.2 Outside Parents and Follow

| Example question | Confirm | Sections |
| --- | --- | --- |
| I want to remove the outside parent of a bone | Target model and bone, whether the bone can have an outside parent | Procedures 4.1 |
| I want to set or change an outside parent | Child and parent model and bone, frames to apply | Procedures 4.2 |
| I want to attach an accessory to a bone of a model | Accessory, model, bone | PMM 8.3, 8.4 |
| I want the camera to follow a bone | Target model and bone | Procedures 4.3 |
| Which models follow which models through outside parents? | | PMM 5.4, 5.6 |

### 3.3 Motion

| Example question | Confirm | Sections |
| --- | --- | --- |
| I want a model's motion as a VMD | Model, whether to limit bones or morphs, encoding of the target environment | Procedures 5.1 |
| I want only a range of frames as a VMD | Start and end frames | Procedures 5.1 (filter the collected keys by frame) |
| I want the camera or light motion as a VMD | Whether to include light and self shadow | Procedures 5.2 |
| I want to load a VMD into a model or the camera | Target, whether to overwrite | Procedures 5.3 |
| I want to extract from a PMM made on Windows in another language | Original encoding, encoding of the target environment | Procedures 2.2, Relations 4.3 |

### 3.4 File Condition

| Example question | Confirm | Sections |
| --- | --- | --- |
| The PMM is broken and will not open; I want at least the contents | How it is broken | Procedures 6.1 to 6.5, 5.1 |
| Objects fly away or values look wrong when opened | | Procedures 6.2 |
| Names or paths are garbled | Language of the system that created the file | Procedures 2.2, 9.3 |
| Is this PMM intact, and can MMD open it safely? | Scope of the checks (all or part) | Validation chapters 1 to 7 |
| Are the name lengths, path lengths and counts within the limits? | | Validation chapters 4 and 5 |
| Is this file a PMM, and which version? | | PMM chapter 4, Appendix A |
| I want a smaller file | | Procedures 9.1 |
| I want to turn physics off | | Procedures 9.2 |
| I want to change the output size, playback range or frame rate | The new values | PMM chapter 4, 9.1 |

### 3.5 Paths and Moving Files

| Example question | Confirm | Sections |
| --- | --- | --- |
| I want a list of the files the PMM uses | Location of the MMD executable folder | Dependencies chapters 2 to 4 |
| I want to gather the PMM and its assets into one folder | Output folder, location of the MMD executable folder | Dependencies chapters 4 and 5 |
| I want models in a folder with one name and motion in a folder with another name | Folder names, how to hand over the motion | Dependencies chapter 5, Procedures 5.1 |
| I want everything needed to be placed in a folder named after the project | Project name, output location | Dependencies 5.2 |
| I want to collect every texture and effect a model refers to | | Dependencies chapters 2 to 4 |
| I moved the whole folder and want the paths to follow | Folders before and after the move | Migration chapter 5 |
| I want to move the project in this folder to another folder | Source and destination, copy or move | Migration chapters 2 to 4 |
| I want to move a project to another computer | Location of the MMD executable folder at the destination | Migration chapter 6 |
| MMD says a model cannot be found | Path in the PMM, how it was opened | Behavior chapter 2, 4.1, Procedures 7.3 |
| Effects were lost after moving to another PC | Whether the EMM exists and its name, paths | Relations 2.1, 2.6, 2.7 |
| Which model is `Pmd3` in the EMM? | | Relations 2.3 |
| Effects shifted after changing render order | | Relations 2.8, Procedures 2.5 |

### 3.6 Creating New Files

| Example question | Confirm | Sections |
| --- | --- | --- |
| I want to create a PMM from a given model and motion | PMX path, VMD path, whether there is a camera VMD, output location | Procedures chapter 8 |
| I want to create an empty PMM | Output location | Procedures 8.2 |

### 3.7 Opening Files in MMD

| Example question | Confirm | Sections |
| --- | --- | --- |
| What is the difference between dropping on the exe, using the menu and dropping on the window, and which is best? | | Behavior chapter 3 |
| Where are relative paths resolved from? | | Behavior chapter 2 |
| MMD says the model structure differs | | Relations 3.2 |
| Why is the EMM loaded when I open a PMM? | | Relations 2.1, 2.2 |
