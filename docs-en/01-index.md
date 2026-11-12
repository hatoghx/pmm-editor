# Index

This material covers the MMD project file (PMM) and the MME assignment file (EMM). It describes the file formats, the relationships between files, how MMD behaves, and step-by-step procedures.

## 1. Documents

| No. | Document | Contents | When to read |
| --- | --- | --- | --- |
| 02 | [Basics](02-basics.md) | Terms, file types, encodings | First |
| 03 | [PMM File Format](03-pmm-format.md) | Binary layout of PMM | When reading or writing PMM |
| 04 | [EMM File Format](04-emm-format.md) | Text layout of EMM | When reading or writing EMM |
| 05 | [VMD File Format](05-vmd-format.md) | Layout of motion files | When extracting or importing motion |
| 06 | [PMX Reference](06-pmx-reference.md) | How PMM lists are built from PMX | When adding a model to a PMM |
| 07 | [File Relationships](07-relations.md) | Correspondence between PMM and EMM, PMX, VMD | When a task spans several files |
| 08 | [MMD Behavior](08-mmd-behavior.md) | Path resolution, differences between ways of opening a PMM | When answering how MMD opens a file |
| 09 | [In-File Operations](09-operations.md) | Duplicate, delete, outside parent, motion, repair, collect, relink, create | When modifying files |
| 10 | [Safety Validation](10-validation.md) | Checks of structure, limits, paths and values | When checking whether a PMM is intact and safe to open |
| 11 | [Packaging](11-dependencies.md) | Dependencies (including second-level references), how to follow references, layout of collected files | When collecting, packaging or organizing assets |
| 12 | [Migration](12-migration.md) | Moving a project elsewhere, rewriting paths | When moving a project to another folder or computer |
| 13 | [Question Guide](13-question-guide.md) | Table from example requests to procedures | When receiving a request |

## 2. Handling a Request

1. Find the matching procedure in the [Question Guide](13-question-guide.md).
2. Read the section of [In-File Operations](09-operations.md) that it points to.
3. Read the format sections (03 to 06) referenced in the procedure.
4. When the request concerns how something looks or opens in MMD, read [MMD Behavior](08-mmd-behavior.md).
5. When the request is to check how broken a PMM is or whether it is safe, read [Safety Validation](10-validation.md).
6. When the request is to collect files, package a project or organize assets, read [Packaging](11-dependencies.md).
7. When the request is to move a project or migrate it to another computer, read [Migration](12-migration.md).

## 3. Section References

The documents refer to sections of other documents with these short names.

| Short name | Document |
| --- | --- |
| Basics | 02 Basics |
| PMM | 03 PMM File Format |
| EMM | 04 EMM File Format |
| VMD | 05 VMD File Format |
| PMX | 06 PMX Reference |
| Relations | 07 File Relationships |
| Behavior | 08 MMD Behavior |
| Procedures | 09 In-File Operations |
| Validation | 10 Safety Validation |
| Dependencies | 11 Packaging |
| Migration | 12 Migration |

For example, "PMM 5.2" means section 5.2 of the PMM File Format.

## 4. Japanese Version

The same content in Japanese is in [docs](../docs/01-index.md).
