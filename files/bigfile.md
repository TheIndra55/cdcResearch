# Bigfile

Bigfiles are archive files used in cdcEngine games to store most of the game files, they usually are multiple aligned files.

## File names

Bigfiles don't contain file names, only a crc32 hash of the file name/path. The hash can be matched to a file name or path like `PC-W\lara.drm`.

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
	uint32_t mask;
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