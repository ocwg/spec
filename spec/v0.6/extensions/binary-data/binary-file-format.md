# OCIF Binary File Format

OCIF files may be stored in a JSON-based text format (`.ocif`) or a binary format (`.ocb`, "OCIF Binary"). With the text format, binary blobs of data may either be base64-encoded within the JSON, or referenced as external files, as defined in [OCIF Binary Data Extension](binary-data.md). The binary format is a more compact representation of the same data within a self-contained file, which appends binary blobs of data after the end of the JSON.

The binary format begins with a 16-byte file header, which contains the following fields:

- A 4-byte magic number.
  - This MUST be equal to the byte sequence `0x4F 0x43 0x49 0x46`, or ASCII string "OCIF".
  - When interpreted as a little-endian unsigned 32-bit integer, this is `0x4649434F`.
- A 4-byte version number.
  - This MUST be equal to the byte sequence `0x00 0x00 0x00 0x00`, or zero.
  - This value is only 0 for the draft version of the specification. The final version will have a different value.
- A 8-byte size number.
  - This MUST be equal to the total size in bytes of the entire file, including the file header, all chunks, all JSON data, and all binary blobs of data.
  - This value is a little-endian unsigned 64-bit integer, with the most significant bit set to zero. The maximum file size of a binary OCIF file is 2^63 - 1 bytes.

After the file header, the file consists of a series of one or more chunks. Each chunk begins with its own 16-byte chunk header, with a similar format to the file header:

- A 4-byte chunk type indicator.
  - In the base specification, this MUST be one of the following:
    - The byte sequence `0x4A 0x53 0x4F 0x4E`, the ASCII string "JSON". This indicates the chunk contains JSON data.
      - When interpreted as a little-endian unsigned 32-bit integer, this is `0x4E4F534A`.
      - JSON chunks MUST be UTF-8 encoded without a BOM, MUST NOT contain control characters `0x7F` or `0x00` through `0x1F` except for optionally tab `0x09` and line feed `0x0A`, and MUST be a valid JSON object. These requirements also apply to the text format.
    - The byte sequence `0x42 0x4C 0x4F 0x42`, the ASCII string "BLOB". This indicates the chunk contains binary blob data, usually the data of a buffer.
      - When interpreted as a little-endian unsigned 32-bit integer, this is `0x424F4C42`.
  - Implementations MAY define additional chunk types, but this is usually not needed. The byte sequence selected SHOULD be a somewhat-human-readable magic sequence of printable ASCII characters, but may be any value. Note: This does not need to match the magic number used by the data format itself, if any.
- A 4-byte chunk compression format indicator.
  - In the base specification, this MUST be one of the following:
    - The byte sequence `0x00 0x00 0x00 0x00`, or zero. This indicates the chunk is not compressed.
    - The byte sequence `0x5A 0x73 0x74 0x64`, the ASCII string "Zstd". This indicates the chunk is compressed using the Zstandard compression format.
      - When interpreted as a little-endian unsigned 32-bit integer, this is `0x6474735A`.
      - The chunk data MUST also include Zstd's own magic number `0x28 0xB5 0x2F 0xFD` at the start of the data, it cannot be omitted.
  - The byte sequence `0xFF 0xFF 0xFF 0xFF` is reserved as an error value, and MUST NOT be used as a compression format indicator, or else the file is invalid.
  - If the chunk represents the data of a buffer, this MUST match the `"compression"` property in the OCIF JSON data for that buffer, with the property undefined for uncompressed data.
  - Implementations MUST support the uncompressed format. Implementations MAY choose to implement any or none of the other compression formats, refusing to load such files.
  - Implementations MAY define additional compression formats. The byte sequence selected SHOULD be a somewhat-human-readable magic sequence of printable ASCII characters, but may be any value. Note: This does not need to match the magic number used by the compression format itself.
- A 8-byte chunk data size number.
  - This MUST be equal to the size in bytes of the chunk data, excluding the chunk header, and excluding any padding after the chunk data.
  - This value is a little-endian unsigned 64-bit integer, with the most significant bit set to zero. The maximum chunk data size is 2^63 - 33 bytes (an additional 32 bytes are subtracted for the file and chunk headers).
  - If the chunk is compressed, this value is the size of the compressed data, not the uncompressed data. However, the `"byteLength"` field in the JSON data refers to the uncompressed size of the data.
- The data in each chunk immediately follows the chunk header, and is of the size in bytes indicated by the chunk data size number.

Every chunk header MUST be aligned to a 16-byte boundary. This means that whenever a chunk has another chunk after it, the chunk on the left MUST have padding placed after the data (not included in the chunk data size) to the next 16-byte boundary (if already at the boundary, there is no padding). The final chunk in the file does not need padding after it. Padding is usually null `0x00` bytes for binary blobs or compressed chunks, but space `0x20` characters SHOULD be used for padding uncompressed JSON data. The 8-byte chunk data size number MUST NOT include this padding, it only includes the used bytes of the chunk data.

The OCIF file header magic number, chunk header type indicators, and chunk header compression format indicators are all [FourCC](https://en.wikipedia.org/wiki/FourCC) codes. This allows for easy identification of the headers if a human inspects the file in a hex editor. Implementations MAY choose to display these values to the user as plain text, such as by showing the compression format indicator in an error message when the compression format is unsupported.

The first chunk in the file MUST be a JSON chunk containing the OCIF JSON data that conforms to the main OCIF schema `schema.json`. If the file has binary blob chunks containing OCIF buffers, they MUST be the following chunks in the file. The OCIF JSON data's buffers array refer to these chunks, such that the buffer at index 0 uses the second chunk if `"uri"` is not defined in that buffer, the buffer at index 1 uses the third chunk if `"uri"` is not defined in that buffer, and so on. This means the `"uri"` property takes precedence over the chunk, so it should be absent in order to use the chunk's bytes. The OCIF JSON buffer's `"byteLength"` always refers to the uncompressed size of the data, but the chunk's data size refers to the compressed size of the data if the chunk is compressed.

All chunks used by buffers MUST immediately follow the first JSON chunk, before any custom chunks. All buffers using chunks MUST be contiguous from the start, so for example, it is not allowed for buffer 0 to use a URI and buffer 1 to use a chunk. The first buffer containing `"uri"` indicates that no buffers beyond that point may use chunks, and any buffers after that point MUST use URIs. The behavior of additional custom chunks not used by buffers is undefined, and may be used for any purpose.

Binary OCIF files smaller than 32 bytes are invalid, because that is the minimum size of the file header and the first chunk header. The minimum possible size of a valid uncompressed OCIF binary file is 57 bytes, which is 32 bytes for the headers and 25 bytes for the minimal JSON data of `{"asset":{"dimension":4}}`. However, such a file contains no data, and is not useful.

Following all of the above rules, the data layout of a OCIF binary file can be summarized as follows:

| Offset in bytes | Size in bytes | Description                                       | Valid values                                     |
| --------------- | ------------- | ------------------------------------------------- | ------------------------------------------------ |
| 0               | 4             | The binary OCIF file header's magic number.       | Constant `OCIF` or `0x4F 0x43 0x49 0x46`         |
| 4               | 4             | The binary OCIF file header's version number.     | Constant based on the spec version               |
| 8               | 8             | The binary OCIF file size in bytes.               | Between 32 and 2^63 - 1                          |
| 16              | 4             | The first chunk's chunk type.                     | Constant `JSON` or `0x4A 0x53 0x4F 0x4E`         |
| 20              | 4             | The first chunk's compression format.             | Constant `0x00000000` for uncompressed           |
| 24              | 8             | The first chunk's size in bytes.                  | Between 0 and 2^63 - 33                          |
| 32              | N             | The first chunk's data, the OCIF JSON data.       | UTF-8 encoded JSON excluding control characters  |
| 32 + N          | P1            | (optional) Padding if a second chunk exists.      | 0 to 15 null bytes or spaces to 16-byte boundary |
| 32 + N + P1     | 4             | (optional) The second chunk's type.               | Constant `BLOB` or `0x42 0x4C 0x4F 0x42`         |
| 36 + N + P1     | 4             | (optional) The second chunk's compression format. | Constant `0x00000000` for uncompressed           |
| 40 + N + P1     | 8             | (optional) The second chunk's size in bytes.      | Between 0 and 2^63 - (48 + N + P1)               |
| 48 + N + P1     | M             | (optional) The second chunk's data.               | Binary blob data                                 |
| 48 + N + M + P1 | P2            | (optional) Padding if a third chunk exists.       | 0 to 15 null bytes or spaces to 16-byte boundary |

More chunks may follow the second chunk, including additional BLOB chunks for buffers, or any other chunk type.

For the file size number, and the size of all chunks, the most significant bit is reserved for future use, and MUST be set to zero. Implementations MUST reject files or chunks where this bit is set to one. This allows for future expansion of the specification to support larger files, such as 128-bit file sizes or beyond, without breaking compatibility, in a similar manner to how UTF-8 extends ASCII.
