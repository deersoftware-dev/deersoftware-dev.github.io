---
layout: default
canonical_url: https://www.deersoftware.dev/labs/khrya/
---

# DeerSoftware + Villa2back // Khrya v1

Khrya is a multimedia book standard developed by DeerSoftware in partnership
with Villa2back, it is based on a list-like system whose objective is to store
objects such as text, images, videos and audios in an orderly and organized way.

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
| 0010  | Book Header       |
| 0011  | HTML              |
| 0100  | Markdown          |
| 0101  | LaTex             |
| 0110  | ReStructured Text |
| 0111  | Texinfo           |
| 1000  | Docbook v4        |
| 1001  | Docbook v5        |

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
| 0110  | Speex       |

#### Video group type

| Value | Description |
| ----- | ----------- |
| 0001  | VP8         |
| 0010  | VP9         |
| 0011  | H.264       |
| 0100  | H.265       |
| 0101  | Theora      |

## Special Objects

Special Objects are a specific category of objects as they have special properties that differentiate them from other types of objects, they may not contain an "object size" field or there may not be a "content" field this depends from object to object and below is their list.

### Null

Null is a special object which has neither "object size", nor "content", its type value will always be 0x00 (0 in decimal or 0000-0000 in binary), its function is to serve as a separator, whether between what would be subordinate objects, separating them or serving to say the end of a Book Header, this object should not be added manually, but added automatically by the implementation in order to perform one of the aforementioned functions.

### Book Header

Book Header is a special object that defines the beginning of a book, in this object not the field "object size", instead the group that determines the size in bits of "object size", is used to determine the size of two big endian integers that is the content of this object, it serves to determine the width and height of the book's pages, exactly in that order.

The end of a Book Header is established when the first Null object that is not separating two possible subordinate objects is found, it is also possible for the same file to have more than one Book Header, in which case, each Book Header should be interpreted as separate chapters from the same book.

## Subordinate objects

Subordinate objects is a type of object that consists of, on the same page of a book, containing more than one associated object, below is how this should work in a more specific way, for two or more objects to be subordinate it is necessary that they are inside a chapter (in the case after a Book Header and before its end) at the same time that there is no Null object between the elements that you want to be subordinated, since it serves to prevent this behavior from happening and that the objects become gather.

### Video -> Audio

When an audio comes after a video, it means that this audio is part of the video and should be played along with the video, even if the codecs are not usually found together, like H.264 and Vorbis, and even if the size is different, It is recommended to use the size of the video as a parameter for the end of the page, but it is up to whoever is implementing the API or using the library.

### Text -> Image

When an image is found after a text, it should be used as the background image for the page, this was created so that book creators can create custom pages with different textures like old paper or parchment.

### Audio -> Image

When an image is found after an audio, it should be displayed during the entire time the audio is played, this was created so that audiobooks can use images to better contextualize for the listener or avoid that the graphical interfaces are completely empty.

## Data storage

It can happen that a book wants to include an image in addition to the background image and for that, every object that is not inside a chapter should not be displayed as a page and should be used as data, this means that the pages can request this specific object to be used as an element, implementations are free to create their own request method in a way that is more consistent with their way of reading the object, but we recommend implementing href resolution with values " khrya:n" for the object that is at position "n".

## Compressing

When we talk about compression algorithm, we recommend XZ compressions for cases that require a high compression and GZIP for cases that require an excellent compression and decompression speed, but we don't limit Khrya to these two, you can use the compression algorithm that best fits for its implementation or project.

## Licensing

[![CC BY-NC-SA 4.0](https://i.creativecommons.org/l/by-nc-sa/4.0/88x31.png)](http://creativecommons.org/licenses/by-nc-sa/4.0/)

This document containing the documentation for the implementation of Khrya has been licensed under the [CC BY-NC-SA 4.0](https://creativecommons.org/licenses/by-nc-sa/4.0/) but that does not mean that its implementation must contain that same license, since it only applies to extensions of the Khrya standard and not the way in which it has been implemented.

## Credits

This standard was developed by [DeerSoftware](https://www.deersoftware.dev/) in partnership with Villa2back.

Developed and written by [Nashira Deer](https://gitlab.com/NashiraDeer).

Reviewed by Pedro A. Costa.
