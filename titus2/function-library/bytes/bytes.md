## Basic Access (3)

| Short Description | Function |
|---|---|
| Length | `bytes.len` | 
| Extract subsequence | `bytes.subseq` |
| Modify subsequence | `bytes.subseqto` |

## Test Validity (6)

| Short Description | Function |
|---|---|
| Verify ASCII format | `bytes.isAscii` |
| Verify LATIN-1 format | `bytes.isLatin1` |
| Verify UTF-8 format | `bytes.isUtf8` |
| Verify UTF-16 format | `bytes.isUtf16` | 
| Verify UTF-16 big endian format | `bytes.isUtf16be` | 
| Verify UTF-16 little endian format | `bytes.isUtf16le` | 

## Decode Bytes to Strings  (6)

| Short Description | Function |
|---|---|
| Decode from ASCII format  | `bytes.decodeAscii` | 
| Decode from LATIN-1 format | `bytes.decodeLatin1` | 
| Decode from UTF-8 format  | `bytes.decodeUtf8` | 
| Decode from UTF-16 format | `bytes.decodeUtf16` |
| Decode from UTF-16 big endian format  | `bytes.decodeUtf16be` | 
| Decode from UTF-16 little endian format  | `bytes.decodeUtf16le` |

## Encode Strings to Bytes (6)

| Short Description | Function |
|---|---|
| Encode to ASCII format | `bytes.encodeAscii` | 
| Encode to LATIN-1 format | `bytes.encodeLatin1` | 
| Encode to UTF-8 format | `bytes.encodeUtf8` | 
| Encode to UTF-16 format | `bytes.encodeUtf16` | 
| Encode to UTF-16 big endian format  | `bytes.encodeUtf16be` | 
| Encode to UTF-16 little endian format  | `bytes.encodeUtf16le` | 

## Base64 Encoding (2)

| Short Description | Function |
|---|---|
| Encode bytes as a base64 string | `bytes.toBase64` | 
| Decode base64 string to bytes | `bytes.fromBase64` | 