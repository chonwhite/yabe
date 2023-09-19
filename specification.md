# Yabe Specification

### Size Encoding Format

#### Format Overview:

The size encoding format efficiently represents sizes, including array and string sizes, as well as other unsigned numbers. It prioritizes smaller sizes commonly encountered in real-world applications to optimize storage space. The format utilizes variable-length encoding, using fewer bytes for smaller sizes while still accommodating larger sizes when needed. By striking a balance between space optimization and scalability, it ensures efficient storage and transmission of size information for various applications.

The size encoding format utilizes a variable number of bytes to represent the size. The number of bytes used depends on the magnitude of the size being encoded. The format follows a scheme where the size is represented in one - five bytes(4).

#### Encoding Rules:

The encoding of the size follows the following rules:

1. If the size value is less than 248, it can be represented in a single byte.
2. If the size value is greater than 248 and less than 503, the first byte is set to 248, and the second byte stores the value minus 248.
3. If the size value is 503 or larger and less than 65536, it is represented in three bytes. The first byte is set to 249, and the following two bytes store the value in little-endian order.
4. If the size value is 65536 or larger and less than 16777216, it is represented in four bytes. The first byte is set to 250, and the following three bytes store the value in little-endian order.
5. If the size value is 16777216 or larger and less than 4294967296, it is represented in five bytes. The first byte is set to 251, and the following four bytes store the value in little-endian order.

By subtracting 248 from small values, the size encoding format can represent a larger range of values within the constraints of two bytes. This optimization allows for efficient representation of small sizes while minimizing the number of bytes required.

#### Encoding Examples
Example 1: Encoding a size value of 150
   - Since 150 is less than 248, apply Rule 1.
   - Single byte: 0x96
   - Encoding: 0x96

Example 2: Encoding a size value of 350
   - Since 350 falls into the range greater than 248 and less than 503, apply Rule 2.
   - Difference: 350 - 248 = 102
   - First byte: 0xF8
   - Second byte: 0x66 (decimal 102)
   - Encoding: 0xF8 0x66

Example 3: Encoding a size value of 1200
   - Since 1200 falls into the range greater than 503 and less than 65536, apply Rule 3.
   - Hexadecimal: 0x4B0
   - First byte: 0xF9
   - Second byte: 0xB0 (least significant byte)
   - Third byte: 0x04 (most significant byte)
   - Encoding: 0xF9 0xB0 0x04

Example 3: Encoding a size value of 120000
   - Since 120000 is greater than 65536 and less than 16777216, apply Rule 4.
   - Hexadecimal: 0x1D4C0
   - First byte: 0xFA
   - Second byte: 0xC0 (least significant byte)
   - Third byte: 0x4C
   - Fourth byte: 0x01 (most significant byte)
   - Encoding: 0xFA 0xC0 0x4C 0x01

Example 4: Encoding a size value of 26777216
   - Since 26777216 falls into the range greater than 16777216 and less than 4294967296, apply Rule 4:
   - Hexadecimal: 0x1999990
   - First byte: 0xFB
   - Second byte: 0x90 (least significant byte)
   - Third byte: 0x99
   - Fourth byte: 0x99
   - Fifth byte: 0x19 (most significant byte)
   - Encoding: 0xFB 0x90 0x99 0x99 0x19

#### Usage:

To utilize the size encoding format, the decoding process involves examining the bytes and reconstructing the original size value. If the first byte is less than 248, it represents the size directly. If the first byte is 249, 250, or 251, the subsequent bytes store the size value minus a predefined offset.

Please note that this size encoding format can accommodate sizes up to 4294967295 (4 bytes). If you require support for larger sizes, you may need to extend the encoding scheme accordingly.

### Array Encoding Format

The array encoding format efficiently represents arrays, including variable-sized and fixed-sized arrays, by utilizing the size encoding format. It supports encoding both the size of the entire array and the sizes of individual elements. The format prioritizes efficient encoding and decoding by first encoding all the size information of each element before encoding their content.

#### Encoding Process

1. Encode the size of the array using the size encoding format.
2. For variable-sized arrays:
   - Iterate through each element in the array.
   - Encode the size of the current element using the size encoding format.
3. Iterate through each element in the array again:
   - Encode the content of the current element.

#### Decoding Process

1. Decode the size of the array using the size encoding format.
2. For variable-sized arrays:
   - Create a list to store the size and offset information of each element.
   - Iterate through the array size count:
     - Decode the size of the current element using the size encoding format.
     - Calculate the offset of the current element based on the sizes and offsets stored in the list.
     - Store the element's size and the calculated offset in the list.
3. Iterate through the list of element sizes and offsets:
   - Decode the content of each element using the corresponding offset.

Here's a general overview of how different data types can be encoded within the array format:

- **Bytes**: Bytes are typically represented using byte arrays. The byte array can be encoded by first encoding the size of the array using the size encoding format, followed by encoding the content of each byte in the array.

- **Strings**: Strings can be represented using string arrays. Similar to byte arrays, the string array can be encoded by encoding the size of the array and then encoding the content of each string element.

- **Arrays**: Arrays, regardless of their element types, can be encoded by encoding the size of the array and then encoding the content of each element. For variable-sized arrays, the size of each element can also be encoded.

- **Objects**: Objects can be represented as arrays of key-value pairs or as structured arrays. Each key-value pair or structured element can be encoded separately, with the keys and values encoded based on their respective data types.

- **Floats**: Floats can be encoded using a specific encoding format, such as the IEEE 754 64-bit double precision format. Float arrays can be represented similarly to other arrays. The encoding process involves encoding the size of the float array using the size encoding format and then encoding the content of each float element using the specified float encoding format.

Integers, booleans, and nulls were left out because they follow different encoding patterns. They will be introduced separately due to their specific encoding schemes, which differ from the array encoding format discussed earlier.
