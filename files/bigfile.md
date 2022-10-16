# Bigfile

Bigfiles are archive files used in cdcEngine games to compose multiple files together, they support compression in some cases.

:::note

The following documentation is about the Bigfile format, not to be confused with the [Tiger Archives](tiger.md) used since Tomb Raider 2013.

:::

## Filenames

Filenames are only stored in form of a hash of the file path, this allows for fast lookup since a binary search is used.

The first games use a custom hash function with the file extension omitted, Defiance and later use CRC32 with a polynomial of `0x4C11DB7`.

## Specialisation

Tomb Raider Legend introduced a new system called specialisation, this allowed storing multiple files with different specialisation masks while keeping the same filename.
This can be used to localize files or load a different file depending on other settings such as Next Generation Content.

The game will do a bitwise AND to match the file with matching specialisation mask.

```c
  10000000000000000000000000000100 German, Next generation content
& 10000000000000000000000000011111 All languages, Next generation content
= 10000000000000000000000000000100
```

A small tool to visualize specialisation masks can be found [here](https://cdcengine.re/specMask.html).

## Alignment

Tomb Raider Legend added the ability for bigfiles to be split over multiple files, the game will rewrite `.dat` extension to the number of the bigfile such as `.004` by aligning the file offset over the bigfile alignment.

You can calculate the bigfile the file is in by using.

```cs
var bigfile = offset / (alignment >> 11);
bigfile.ToString("000");
```

## Format

This section will give a quick overview of the structure of the bigfile per game, the supported games will be shown by the game icon.

### Soul Reaver 2

![Soul Reaver 2](../images/icons/sr2.jpg) ![Legacy of Kain Defiance](../images/icons/sr3.jpg)

```cpp
struct BigFileEntry
{
    uint32_t fileLen;
    uint32_t filePos;
    uint32_t compressedLen;
}

struct BigFile
{
    uint16_t numFiles;
    uint16_t pad;  

    // list of filename hashes, position in this array will match position in contents array
    uint32_t hashes[numFiles];

    BigFileEntry contents[numFiles];
}
```

### Tomb Raider Legend

![Tomb Raider Legend](../images/icons/tr7.jpg) ![Tomb Raider Anniversary](../images/icons/trae.jpg) ![Tomb Raider Underworld](../images/icons/tr8.jpg) ![Guardian of Light](../images/icons/lc1.jpg)

```cpp
struct ArchiveRecord
{
	uint32_t size;
	uint32_t offset;
	uint32_t specMask; // specialisation mask
	int32_t compressedLength;
}

struct ArchiveFile
{
	uint32_t numRecords;
	
	// list of filename hashes, position in this array will match position in records array
	uint32_t hashes[numRecords];
	
	// all file records
	ArchiveRecord records[numRecords];
}
```

### Deus Ex Human Revolution

![Deus Ex Human Revolution](../images/icons/dx3.jpg)

```cpp
struct ArchiveRecord
{
	uint32_t size;
	uint32_t offset;
	uint32_t specMask; // specialisation mask
	int32_t compressedLength;
}

struct ArchiveFile
{
    uint32_t alignment;
    char configName[64] // config name/build dir such as pc-w, xenon-w

	uint32_t numRecords;
	
	// list of filename hashes, position in this array will match position in records array
	uint32_t hashes[numRecords];
	
	// all file records
	ArchiveRecord records[numRecords];
}
```

## Tools

- Gibbed
- [Yura](../tools/yura.md)
- [Soul Spiral](https://www.thelostworlds.net/Software/Soul_Spiral.html)
- TRLTool