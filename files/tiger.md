# Tiger Archive

Tiger files are the newer archive files used since Tomb Raider 2013, they are the replacement for [bigfiles](bigfile.md).

## Specialisation

See [Specialisation](bigfile.md#specialisation) in the bigfile format.

## Offset

The offset in the file entries is a bitfield, with x bits being the index of the archive file and other bits being the offset in the archive.
See the description at each version for the actual number of bits for each value.

## Format

The file will start with a magic and version number, the magic is TAFS (Tiger Archive FileSystem) or 0x53464154.

```cpp
struct TigerArchive
{
    uint32_t magic;
    uint32_t version;
}
```

### Version 3

Used in Tomb Raider 2013. For packedOffset the first 4 bits is the archive index, last 21 is the offset of the file.

```cpp
struct TigerArchiveEntry
{
    uint32_t hash; // filename hash
    uint32_t specMask; // specialisation mask
    uint32_t size;
    uint32_t packedOffset;
}

struct TigerArchive
{
    uint32_t magic;
    uint32_t version;

    uint32_t numArchives;
    uint32_t numRecords;
    uint32_t dlcIndex;

    // config name/build dir such as pcx64-w, scarlett-w
    char configName[32];

    // all file records
    TigerArchiveEntry records[numRecords];
}
```

### Version 4

Used in Rise of the Tomb Raider. For packedOffset the first word (16 bits) is the archive index, the last dword (32 bits) is the file offset.

```cpp
struct TigerArchiveEntry
{
    uint32_t hash; // filename hash
    uint32_t specMask; // specialisation mask
    uint32_t size;
    uint32_t pad;
    uint64_t packedOffset;
}

struct TigerArchive
{
    uint32_t magic;
    uint32_t version;

    uint32_t numArchives;
    uint32_t numRecords;
    uint32_t dlcIndex;

    // config name/build dir such as pcx64-w, scarlett-w
    char configName[32];

    // all file records
    TigerArchiveEntry records[numRecords];
}
```

### Version 5

Version 5 switched to a 64-bit hash with the FNV-1A hashing algorithm. For packedOffset the first word (16 bits) is the archive index, the last dword (32 bits) is the file offset.

```cpp
struct TigerArchiveEntry
{
    uint64_t hash; // filename hash
    uint64_t specMask; // specialisation mask
    uint32_t size;
    uint32_t unknown;
    uint64_t packedOffset;
}

struct TigerArchive
{
    uint32_t magic;
    uint32_t version;

    uint32_t numArchives;
    uint32_t numRecords;
    uint32_t dlcIndex;
    uint32_t unknown;

    // config name/build dir such as pcx64-w, scarlett-w
    char configName[32];

    // all file records
    TigerArchiveEntry records[numRecords];
}
```