> Corona-X Preliminary API A.1<br>
> Tyler Besselman<br>
> 26.11.2015<br>

[TOC]


#CAR Format X.F

This is the final format for a CAR Archive. It supersedes all previous formats for the archive and is the definitive specification for CAR Archive Files. A CAR Archive is meant to represent a single directory and all files contained within it using a single file/archive.

##Format

CAR Archives are lists of files and corresponding data. All forms of a CAR Archive include information to ensure enclosed data is not corrupted. They may also optionally include metadata that allows for signing of the file, encrypting archived contents, and compression.

Paths in this format are separated not by `/` or `\`, but rather by the `:` character. This allows the characters `/` and `\` to be used in path names. If the `:` character is used in a path name, it may be replaced by the unicode character `0xEEEE` in order to denote a `:` in the final path. Paths in archives are all relative to an arbitrary root directory at path `@`. All paths are specified as either in the `@` directory or one of its subdirectories. No files may be outside the `@` directory, and while there is no symbolic link to the current directory (`.`) or its parent directory (`..`), the filenames `.` and `..` are still prohibited in archives.

Implementation Note: All numerical values in CAR archives should be in **little endian** format.

Note: For the archive's encryption and compression algorithms to work together correctly, the header and data modification sections must be left intact in the compressed/encrypted header.

---
###Subtype 1 --- Base Format

This is the simplified format of the CAR Archive header. It provides only the basic file storage format without any of the extensions. An archive is a direct translation of a directory as it appeared on disk at the time of the archive's creation with no attached metadata.
```
struct CARHeaderS1 {
    UInt8  magic[4];   // 'C', 'A', 'R', '\0'
    UInt8  version[4]; // 'X', '.', 'F', '1'
    UInt64 entryTableOffset;
    UInt64 dataSectionOffset;
    UInt32 dataChecksum;
    UInt32 headerChecksum;
}; // 32 bytes
```

The `checksum` field of this header should be set to the CRC-32 checksum of all data in the file except the header.
The `headerChecksum` should be set to the CRC-32 checksum of all data in the header except the `headerChecksum` field.

####Table of Contents

The archive's Table of Contents (TOC) is a list of all the files in the archive. The table of contents is simply an array of pointers to entries in the archive's entry table. These pointers are 64-bit offsets into the entry table. The table of contents directly follows the archive header.

####Entry Table

The archive's entry table is simply an array of entries. It specifies each file's path relative to the directory represented by the archive, the type of file, the offset into the data section where the file's data is stored, and the size of the file's data. Each entry in this table is of variable length. The entries are stored next to each other from the start offset to right before the start of the data section. Each entry is as follows:
```
struct CAREntry {
    UInt8 type;
    UInt8 padding[3];
    UInt64 dataOffset;
    UInt64 dataSize;
    UTF8String *path;
}; // 20 + path bytes
```

Valid values for the `type` property of the entry are as follows:
```
enum CARTOCEntryType {
    kCARTOCEntryTypeFile         = 0,
    kCARTOCEntryTypeDirectory    = 1,
    kCARTOCEntryTypeLink         = 2
};
```

The file entry type is a regular file. The data of the file represented by the entry is stored at `dataOffset` and is `dataSize` bytes long.

The `dataOffset` and `dataSize` fields are unused for directory entires. A directory entry simply represents an abstract directory.

The link entry type can either represent a symbolic or a hard link to a file. A symbolic link looks a lot like a file entry, using the `dataOffset` and `dataSize` entries to point into the data section. The difference is that the data references is the directory-relative path of the symbolic link. A hard link, on the other hand, has a `dataSize` field which is set to 0 and a `dataOffset` which points to the table of contents index of the entry which stores file to which the hard link references.

The `path` field is the UTF-8 encoded path for the given file. This field is 8-byte aligned on a 4-byte boundary (0x4 and 0xC as opposed to 0x0 and 0x8). Padding can simply be made of null bytes.

The entry table includes four null bytes at its onset to bring the alignment to a 4-byte boundary.

####Data Section

This section includes the raw data of all the files in the archive. The offset of this section in the archive is specified in the archive's header. The method through which data is stored in this archive is largely unspecified. The only specified behavior of this section is that each `dataOffset` in each table of contents entry points to the `dataSize` bytes constituting the entirety of the file at the path given by the same entry (for a regular file entry).

###Subtype 2 --- Extended Format

This header provides the necessary extensions to the standard header to allow for all of the aforementioned archive uses. This header introduces 2 new sections: data modification and archive signature. The data modification section allows the archive to be compressed or encrypted and the archive signature section allows the archive to be digitally signed. The version number of this format is "X.F2".
```
struct CARHeaderS2 {
    UInt8  magic[4];   // 'C', 'A', 'R', '\0'
    UInt8  version[4]; // 'X', '.', 'F', '2'
    UInt64 tocOffset;
    UInt64 entryTableOffset;
    UInt64 dataSectionOffset;
    UInt32 dataChecksum;
    UInt32 headerChecksum;
    UInt64 dataModification;
    UInt64 archiveSignature;
}; // 56 bytes
```

####Table of Contents

The Table of Contents format is identical to subtype 1.

####Entry Table

The layout of this table is the same as in subtype 1 except the entry format has been changed as follows (each entry is still 4-byte aligned):

```
struct CAREntry {
    UInt8 type;
    UInt8 flags;
    UInt16 padding;
    UInt64 dataOffset;
    UInt64 dataSize;
    UnicodeData path[];
}; // 20 + path bytes
```

Another entry type is added for this subtype. This type is `kCARTOCEntryTypeMeta` with a value of `0xFF`. These entries are not meant to be extracted from the archive; they give metadata about other entries and the archive as a whole.

If the type of the entry is meta, the `dataOffset` and `dataSize` entries are only present if the flag `0x80` is set in the `flags` field (`kCAREntryFlagMetaHasData`).

The `dataOffset` and `dataSize` entries are not present for directories.

The 3 least significant bits of the `flags` field designate the path encoding used for the entry. A value of `0x0` designates UTF-8, a value of `0x1` designates UTF-16, and a value of `0x2` designates UTF-32.

The `path` field is a string in the encoding specified by the `flags` field. This field is aligned in the same way as in subtype-1. The entry table is also aligned in the same way.

####Data Modification Section

The format of the Data Modification Section is as follows:
```
struct CARDataModification {
	UInt8 encryptionCount;
	UInt8 compressionCount;
	UInt8 padding[6];
}; // 8 bytes

struct CAREncryptionInfo {
    UInt64 startOffset;
    UInt64 runLength;
    UInt8 type;
    UInt8 padding[7];
}; // 24 bytes

struct CARCompressionInfo {
    UInt64 startOffset;
    UInt64 runLength;
    UInt8 type;
    UInt8 padding[7];
}; // 24 bytes
```

The header points to the archive's `CARDataModification` table. Immediately following this table is `encryptionCount` fields of type `CAREncryptionInfo` and `compressionCount` fields of type `CARCompressionInfo`. These fields are meant to be processed in the order they appear.

Further specification of this section is yet to come. CAR Archives will likely use LZMA2 and LZO compression with AES and Serpent encryption.

####Archive Signature Section

**Unspecified**

I didn't buy the X.509 specification, and that's what I'd like to use here. Once I get access to that specification, I will complete this section. For now, just skip this section in parsing and don't write it when creating archives.

###Subtype 3 --- Mutable Archive

**Unspecified --- I may remove this, as it's not really the purpose of a CAR Archive**

---

#Special Forms of CAR Archives

##BootX

###Purpose

This form of CAR Archive is used to boot up the CXK. It is meant to be used as a type of kernel ramdisk. It is a signed, compressed, and optionally encrypted CAR Archive with minor changes to the archive header. The bootloader is meant to load this file directly into memory from its filesystem location, ensure that the archive is for the correct processor and bootloader, parse the BootConfig from the archive, and finally call the entry method in the included kernel with the necessary boot arguments.

###Format Changes

The format for BootX is based on the standard Type 2 archive.

The header is a modified as follows:
```
struct CARHeaderBootX {
    UInt8  magic[4];   // 'C', 'A', 'R', '\0'
    UInt8  version[4]; // 'X', '.', 'F', 'b'
    UInt32 bootID;
    UInt16 processorType;
    UInt16 lockA; // set to 0xEFFE

    UInt64 entryTableOffset;
    UInt64 dataSectionOffset;
    UInt32 dataChecksum;
    UInt32 headerChecksum;

    UInt16 kernelLoaderEntry;
    UInt16 kernelEntry;
    UInt16 bootConfigEntry;

    UInt16 systemServerEntry;
    UInt16 ioServerEntry;
    UInt16 faultServerEntry;
    UInt16 extensionDirectory;

    UInt16 lockB; // Set to 0xFEEF
}; // 56 bytes
```

Additionally, the Signature Section **must** appear directly after this header and **must** be directly followed by the Data Modification Section and the archive's table of contents.

The `processorType` section of the file's header must hold one of the following CPU architecture values:
```
enum CARProcessorType {
    kCARProcessorTypeX86    = 0x0000,
    kCARProcessorTypeX86_64 = 0x1000,
    kCARProcessorTypeARMv7  = 0x0001,
    kCARProcessorTypeARMv8  = 0x1001
};
```

All sections in this format must be either 32 or 64 bit aligned, depending on the type of processor the enclosed kernelspace is meant to be run on. Given that Corona-X only supports a 64-bit kernelspace, all sections in this format should be 64-bit aligned.

---

System
------

###Purpose

This form of CAR Archive is used for the System Partition on CX systems. The modifications which have been made allow for certain information not normally supported by the CAR Format to be provided. This archive format is not meant to be a file within a filesystem, but rather it is meant to be written directly to disk as an unmodifiable filesystem. As such, all sections of this archive should be 512-byte aligned. The root path in this archive represents the root of the filesystem, and subdirectories are simply subdirectories of the filesystem root node. This filesystem type is immutable, and editing is only possible through overwriting the entire archive on disk. The filesystem version of CAR Archive must includes a signature for validation of distribution authority. The GPT GUID for a CAR System Partition is `27FCA2BA-F3CB-548D-8619-319D4992A7F7`.

###Format Changes

This archive is based on the Type 2 CAR Archive.
The modifications are as follows:
```
struct CARHeaderSystem {
    UInt8  magic[4];   // 'C', 'A', 'R', '\0'
    UInt8  version[4]; // 'X', '.', 'F', 's'

    struct CARSystemVersionInternal {
        UInt64 type:2;         // OS Type
        UInt64 majorVersion:7; // Major Version Number
        UInt64 revision:5;     // OS Revision (English Letter)
        UInt64 buildType:2;    // OS Build Type
        UInt64 buildID:48;     // System Build ID
    } systemVersion;

    UInt64 tocOffset;
    UInt64 entryTableOffset;
    UInt64 dataSectionOffset;
    UInt32 dataChecksum;
    UInt32 headerChecksum;
    UInt64 dataModification;
    UInt64 archiveSignature;
}; // 64 bytes
```
Where valid OS Types for `type` as follows:
```
enum CARSystemType {
    kCARSystemTypeCoronaX = 0, // 00
    kCARSystemTypeCorOS   = 1, // 01
    kCARSystemTypeXMobile = 2, // 10
    kCARSystemTypeCMobile = 3  // 11
};
```
And valid OS Build Types for `buildType` are:
```
enum CARSystemBuildType {
    kCARSystemBuildTypeDebug       = 0, // 00
    kCARSystemBuildTypeDevelopment = 1, // 01
    kCARSystemBuildTypeRelease     = 2, // 10
    kCARSystemBuildTypeStable      = 3  // 11
};
```
<br><br>
Additionally, the format of the archive's entry table has been changed depending on the entry's `type` field.

Directory Entries have the following format:
```
struct CARSystemDirectoryEntry {
    UInt8 type;
    UInt8 flags;
    UInt16 specialFlags;
    UInt8 padding;

    UInt32 parentEntry; // Parent directory
    UInt32 nextEntry;   // Next Entry in parent
    UInt32 entryCount;  // Number of entries
    UInt32 firstEntry;  // First entry

    UnicodeData path[];
}; // 24 + path bytes
```
Possible values for `specialFlags` include:
```
enum CARSystemDirectoryFlag {
    kCARSystemDirectoryFlagCanEnumerate  = 0x8000, // Can list files in this directory
    kCARSystemDirectoryFlagAllowsExecute = 0x4000, // Can execute files in this directory (execute flag)
};
```

Directories employ a linked list for entries, with the head of the list pointed to in the `firstEntry` field. As file entries have the same initial structure, reading this list becomes almost trivial, and generating a cache for the file system should become easier.

And file entries (which are identical to link entries) have the following format:
```
struct CARSystemFileEntry {
    UInt8 type;
    UInt8 flags;
    UInt16 specialFlags;
    UInt8 padding;

    UInt32 parentEntry; // Parent directory
    UInt32 nextEntry;   // Next Entry in parent
    UInt64 dataOffset;
    UInt64 dataSize;

    UnicodeData path[];
}; // 30 + path bytes
```

with the `specialFlags` entry holding a set of the following bitmasks:
```
enum CARSystemFileFlag {
    kCARSystemFileFlagCanRead     = 0x0001, // Allow privileged users to read the contents of this file
    kCARSystemFileFlagCanUserRead = 0x0002, // Allow regular users to read the contents of this file
    kCARSystemFileCanExecute      = 0x0004, // Allow privileged users to execute this file
    kCARSystemFileCanUserExecute  = 0x0010, // Allow regular users to execute this file
}
```

---

CX Application
--------------

**Unspecified**

---

CX Package
----------

**Unspecified**

---
