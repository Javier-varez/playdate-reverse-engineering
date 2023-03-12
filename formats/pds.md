A file with the `.pds` extension represents a collection localization strings that have been compiled by `pdc`. This format uses little endian byte order.

## Header

| Offset | Type     | Detail |
|:-------|:---------|:-------|
| `0`    | `char[12]` | Ident "Playdate STR" |
| `12`   | `uint32`   | File bitflags        |

### File Flags

| Bitmask             | Detail                                      |
|:--------------------|:--------------------------------------------|
| `flag & 0x80000000` | If `> 0`, the data in this file is compressed |

### String header

If the compression flag is set, there's an extra string data header after the file header. Everything after this is zlib-compressed. 

| Offset | Type     | Detail |
|:-------|:---------|:-------|
| `0`   | `uint32`  | Size of decompressed string data |
| `4`   | `uint32`  | Unused/reserved, seen as 0 |
| `8`   | `uint32`  | Unused/reserved, seen as 0 |
| `12`  | `uint32`  | Unused/reserved, seen as 0 |

## String Data

### Table Header

| Offset | Type    | Detail |
|:-------|:--------|:-------|
| `0`    | `uint32` | Number of string entries |

### Table

After this header, there is a table of int32 offsets for each string entry aside from the first one, as well as an offset to the end of the data.

Offsets are relative to the end of the table, and the first string entry always begins directly after the table.

### String Entries

Each string entry contains an utf8 string key, followed by a null byte, followed by a utf8 string value, followed by another null byte.