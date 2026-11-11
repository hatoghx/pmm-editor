# Index

This material covers the MikuMikuDance project file (PMM) and the MikuMikuEffect assignment file (EMM). It describes the file formats, the relationships between files, how MikuMikuDance behaves, and step-by-step procedures.

## 1. Documents

| No. | Document | Contents | When to read |
| --- | --- | --- | --- |
| 01 | [Basics](01-basics.md) | Terms, file types, encodings | First |
| 02 | [PMM File Format](02-pmm-format.md) | Binary layout of PMM | When reading or writing PMM |
| 03 | [EMM File Format](03-emm-format.md) | Text layout of EMM | When reading or writing EMM |
| 04 | [VMD File Format](04-vmd-format.md) | Layout of motion files | When extracting or importing motion |
| 05 | [PMX Reference](05-pmx-reference.md) | How PMM lists are built from PMX | When adding a model to a PMM |
| 06 | [File Relationships](06-relations.md) | Correspondence between PMM and EMM, PMX, VMD | When a task spans several files |
| 07 | [MMD Behavior](07-mmd-behavior.md) | Path resolution, differences between ways of opening a PMM | When answering how MMD opens a file |
| 08 | [Procedures](08-operations.md) | Duplicate, delete, outside parent, motion, repair, collect, relink, create | When modifying files |
| 09 | [Question Guide](09-question-guide.md) | Table from example requests to procedures | When receiving a request |
| 10 | [Verification](10-verification.md) | Evidence, observed values, unconfirmed items | When checking how reliable a statement is |

## 2. Handling a Request

1. Find the matching procedure in the [Question Guide](09-question-guide.md).
2. Read the section of [Procedures](08-operations.md) that it points to.
3. Read the format sections (02 to 05) referenced in the procedure.
4. When the request concerns how something looks or opens in MikuMikuDance, read [MMD Behavior](07-mmd-behavior.md).
5. When a statement is marked "unconfirmed", check chapter 5 of [Verification](10-verification.md) and tell the user.

## 3. Section References

The documents refer to sections of other documents with these short names.

| Short name | Document |
| --- | --- |
| Basics | 01 Basics |
| PMM | 02 PMM File Format |
| EMM | 03 EMM File Format |
| VMD | 04 VMD File Format |
| PMX | 05 PMX Reference |
| Relations | 06 File Relationships |
| Behavior | 07 MMD Behavior |
| Procedures | 08 Procedures |

For example, "PMM 5.2" means section 5.2 of the PMM File Format.

## 4. Japanese Version

The same content in Japanese is in [docs](../docs/00-index.md).
