# Data Ram

Data ram files are files containing sections of structures and data (such as audio, textures). General sections are loaded and relocated right into memory, other section types are passed to special functions e.g. to load the texture or audio.

## Header

The file starts with the `SectionList` header containing all sections. After that all section data in the same order as in the header, relocations first then the section data.

```cpp
struct SectionInfo
{
	int32_t size; // size of the section data, this is without relocations
	uint8_t sectionType;
	uint8_t pad;
	uint16_t versionID;
	uint32_t packedData; // see below
	uint32_t id; // if section type is not data then this is the id e.g. texture id or animation id
	uint32_t specMask;
}

struct SectionList // drm file
{
	int32_t version;
	int32_t numSections;
	SectionInfo sections[numSections];
}
```

The `packedData` member contains a couple of bitfields, the last 24 bits are the number of relocations.

```cpp
auto numRelocations = packedData >> 8;
```

## Relocations

Relocations are used for resolving pointers at runtime and for pointers to other sections. The relocation data is located before every section.

```cpp
struct Relocation
{
	uint16_t typeAndSectionInfo;
	int16_t typeSpecific;
	uint32_t offset;
}

Relocation relocations[numRelocations]
```

For each pointer there is a relocation, the last 13 bits of `typeAndSectionInfo` is the index of the section the pointer should be pointing to (can also be the same section). `offset` is the offset in the section data where it should be patched.

## Section types

Besides general data sections containing structures there can also be sections with other data like textures.

Whereas normal sections are just returned, other section types will be processed. Like all textures sections are loaded as texture.

| # | Section type |
|---|--------------|
| 0 | Data |
| 2 | Animation |
| 5 | Texture |
| 6 | Wave |
| 7 | DTPData |