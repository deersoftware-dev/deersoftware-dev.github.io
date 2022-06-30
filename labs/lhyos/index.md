---
layout: default
canonical_url: https://www.deersoftware.dev/labs/lhyos/
---

# DeerSoftware + Villa2back // Lhyos v1

Lhyos is a multimedia book standard developed by DeerSoftware in partnership
with Villa2back, it is based on a list-like system whose objective is to store
objects such as text, images, videos and audios in an orderly and organized way.
Its name comes from the junction of the initials of human objects and the word
lys or list in Afrikaans.

## Structuring

> [file version] [object list...]

| Name         | Description                                               | Size         |
| ------------ | --------------------------------------------------------- | ------------ |
| file version | Version of the standard used for writing the file.        | 1 byte       |
| object list  | The list of objects following the structure of "Objects". | Undetermined |

## Objects

> [object type] [object size] [object content]

| Name           | Description                                         | Size                        |
| -------------- | --------------------------------------------------- | --------------------------- |
| object type    | Specifies how the next bytes are to be interpreted. | 1 byte                      |
| object size    | Size in bytes of "object content".                  | Determined by "object type" |
| object content | Object content.                                     | Determined by "object size" |

### Object type

The byte representing the object's type is divided into 3 parts, which are
2 bits long in the case of the first and second parts and 4 bits in the
third part. The first part is responsible for defining the size of
"object size", the second for defining the type group and the third the type.

#### First part

| Value | Description             |
| ----- | ----------------------- |
| 00    | 8-bit number (1 byte)   |
| 01    | 16-bit number (2 bytes) |
| 10    | 32-bit number (4 bytes) |
| 11    | 64-bit number (8 bytes) |

#### Second part

| Value | Description |
| ----- | ----------- |
| 00    | Text        |
| 01    | Image       |
| 10    | Audio       |
| 11    | Video       |

#### Third part // Text

#### Third part // Image

#### Third part // Audio

#### Third part // Video

### Object size

## Counting objects

## Videos with audio

## Compressing
