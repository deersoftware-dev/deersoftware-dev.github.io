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

## Root

The root or base of the file consists of a byte that determines the API version of the file followed by the list of objects, if the file has been formatted using the API standard specified in this document, this byte must have a value of 0x00 (0).

The reason there is this byte specifying the version of the file is backward compatibility, so that the same database can have different versions of the standard, being able to create objects in a more current version, but without having to update the existing ones.

> [file version] [object list...]

| Name         | Description                                               | Size         |
| ------------ | --------------------------------------------------------- | ------------ |
| file version | Version of the standard used for writing the file.        | 1 byte       |
| object list  | The list of objects following the structure of "Objects". | Undetermined |

## Objects

The objects are formed by a set of header and body with the exception of some objects, the special objects. The header consists of a byte followed by a group that can be 1, 2, 4 or 8 bytes in size depending on the value of the first byte group quoted above (More information below), this group of assorted bytes is nothing but a integer in big endian format that expresses the size of the body, which would be the content of this object.

> [object type] [object size] [object content]

| Name           | Description                                         | Size                        |
| -------------- | --------------------------------------------------- | --------------------------- |
| object type    | Specifies how the next bytes are to be interpreted. | 1 byte                      |
| object size    | Size in bytes of "object content".                  | Determined by "object type" |
| object content | Object content.                                     | Determined by "object size" |

## Object type

A byte is a set formed by 8 bits and for better use of memory, we decided to divide a byte into 3 groups, having 2, 2 and 4 bits in size respectively, below is the table of values that each group can have and their meanings.

> [first group] [second group] [third group]

### First group

| Value | Description             |
| ----- | ----------------------- |
| 00    | 8-bit number (1 byte)   |
| 01    | 16-bit number (2 bytes) |
| 10    | 32-bit number (4 bytes) |
| 11    | 64-bit number (8 bytes) |

### Second group

| Value | Description |
| ----- | ----------- |
| 00    | Text        |
| 01    | Image       |
| 10    | Audio       |
| 11    | Video       |

### Third group

In the third group, its meaning of its value depends exclusively on the value of the second group, just as the type of the object size depends on the value of the first group.

#### Text group type

| Value | Description       |
| ----- | ----------------- |
| 0001  | Plain Text        |
| 0010  | HTML              |
| 0011  | Markdown          |
| 0100  | LaTex             |
| 0101  | ReStructured Text |
| 0110  | Texinfo           |
| 0111  | Docbook v4        |
| 1000  | Docbook v5        |

#### Image group type

| Value | Description |
| ----- | ----------- |
| 0001  | PNG         |
| 0010  | JPEG        |
| 0011  | SVG         |
| 0100  | GIF         |
| 0101  | WebP        |

#### Audio group type

| Value | Description |
| ----- | ----------- |
| 0001  | Opus        |
| 0010  | Vorbis      |
| 0011  | FLAC        |
| 0100  | MP3         |
| 0101  | AAC         |

#### Video group type

| Value | Description |
| ----- | ----------- |
| 0001  | VP8         |
| 0010  | VP9         |
| 0011  | H.264       |
| 0100  | H.265       |

## Counting objects

LHYOS does not prohibit the storage of empty objects, on the contrary, it even uses them as you see in "Videos with audio" and for better control of these objects the implementations of this LHYOS standard need to have two types of object count, the real count that counts objects empty and the virtual or default count, which does not count, the same thing should be in query systems, where real queries should return the object even if empty, while virtual or default queries do not.

## Videos with audio

In the case of videos with audio, the LHYOS standard defines that every audio object after a video must be interpreted as the audio of that video, unless there is an empty object between them thus separating them.

## Compressing

When we talk about compression algorithm, we recommend XZ compressions for cases that require a high compression and GZIP for cases that require an excellent compression and decompression speed, but we don't limit LHYOS to these two, you can use the compression algorithm that best fits for its implementation or project.

## Licensing

This document containing the documentation for the implementation of LHYOS has been licensed under the [GPL 3.0 license](https://www.gnu.org/licenses/gpl-3.0.txt) but that does not mean that its implementation must contain that same license, since it only applies to extensions of the LHYOS standard and not the way in which it has been implemented.

## Credits

This standard was developed by [DeerSoftware](https://www.deersoftware.dev/) in partnership with Villa2back.

Developed and written by [Nashira Deer](https://gitlab.com/NashiraDeer).

Reviewed by Pedro A. Costa.
