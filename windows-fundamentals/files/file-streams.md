A stream is a sequence of bytes. 

NTFS stores a file as a collection of attributes inside a record in the Master File Table. A typical file record looks something like
```
MFT record for hm.txt
├── $STANDARD_INFORMATION
├── $FILE_NAME
├── unnamed $DATA
└── named $DATA called "secret"
```

Each stream in a file has 
- The allocation size is the amount of disk space that is reserved for a stream.
- The actual size is the number of bytes that are being used by a caller.
- The valid data length (VDL) is the number of bytes that are initialized from the allocation size for the stream.

The full name of a stream is `_filename_:_stream name_:_stream type` . Users can only use existing stream types. The default data stream is unnamed. 

The following are possible stream types

| Stream Type              | Description                                                                                                                                                                                                                                                                                                                                                                                                                                   |
| ------------------------ | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| ::$ATTRIBUTE_LIST        | Contains a list of all attributes that make up the file and identifies where each attribute is located.                                                                                                                                                                                                                                                                                                                                       |
| ::$BITMAP                | A bitmap used by indexes to manage the b-tree free space for a directory. The b-tree is managed in 4 KB chunks (regardless of cluster size) and this is used to manage the allocation of these chunks. This stream type is present on every directory.                                                                                                                                                                                        |
| ::$DATA                  | Data stream. The default data stream has no name. Data streams can be enumerated using the [FindFirstStreamW](https://learn.microsoft.com/en-us/windows/win32/api/fileapi/nf-fileapi-findfirststreamw) and [FindNextStreamW](https://learn.microsoft.com/en-us/windows/win32/api/fileapi/nf-fileapi-findnextstreamw) functions.                                                                                                               |
| ::$EA                    | Contains Extended Attributes data.                                                                                                                                                                                                                                                                                                                                                                                                            |
| ::$EA_INFORMATION        | Contains support information about the Extended Attributes.                                                                                                                                                                                                                                                                                                                                                                                   |
| ::$FILE_NAME             | The name of the file, in Unicode characters. This includes the short name of the file as well as any hard links.                                                                                                                                                                                                                                                                                                                              |
| ::$INDEX_ALLOCATION      | The stream type of a directory. Used to implement filename allocation for large directories. This stream represents the directory itself and contains all of the data of the directory. Changes to streams of this type are logged to the NTFS change journal. The default stream name of an $INDEX_ALLOCATION stream type is $I30 so "_DirName_", "_DirName_::$INDEX_ALLOCATION", and "_DirName_:$I30:$INDEX_ALLOCATION" are all equivalent. |
| ::$INDEX_ROOT            | This stream represents root of the b-tree of an index. This stream type is present on every directory.                                                                                                                                                                                                                                                                                                                                        |
| ::$LOGGED_UTILITY_STREAM | Similar to ::$DATA but operations are logged to the NTFS change journal. Used by EFS and [Transactional NTFS (TxF)](https://learn.microsoft.com/en-us/windows/win32/fileio/transactional-ntfs-portal). The ":_StreamName_:$_StreamType_" pair for EFS is ":$EFS:$LOGGED_UTILITY_STREAM" and for TxF is ":$TXF_DATA:$LOGGED_UTILITY_STREAM".                                                                                                   |
| ::$OBJECT_ID             | An 16-byte ID used to identify the file for the link-tracking service.                                                                                                                                                                                                                                                                                                                                                                        |
| ::$REPARSE_POINT         | The [reparse point](https://learn.microsoft.com/en-us/windows/win32/fileio/reparse-points) data.                                                                                                                                                                                                                                                                                                                                              |

```shell
# display alternate streams on Windows
dir /r
```

