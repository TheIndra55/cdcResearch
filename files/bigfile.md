# Bigfile

Bigfiles are archive files used in cdcEngine games to store most of the game files, they usually are multiple aligned files.

:::note

The following documentation is about the bigfile format between Tomb Raider Legend and The Guardian of Light.
Newer games make use of the Tiger Archive format which differs from this apart from some concepts.

:::

## File names

Bigfiles don't contain file names, only a crc32 hash of the file name/path. The hash can be matched to a file name or path like `PC-W\lara.drm`.

## Specialisation

A filename can exist multiple times in the archive, for example for different languages or content. For this the specialisation mask is used,
the game will do a bitwise AND to match the file with matching specialisation mask.

```c
  10000000000000000000000000000100 German, Next generation content
& 10000000000000000000000000011111 All languages, Next generation content
= 10000000000000000000000000000100
```

A small tool to visualize specialisation masks can be found [here](https://cdcengine.re/specMask.html).

## Alignment

If the bigfile extension ends with `.000` then the offset of the file can be aligned over multiple bigfiles. You can calculate the bigfile the file is in by using.
```cs
var bigfile = offset / (alignment >> 11);
bigfile.ToString("000");
```

## Header

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
	
	// list of file name hashes, position in this array will match position in records array
	uint32_t hashes[numRecords];
	
	// all file records
	ArchiveRecord records[numRecords];
}
```