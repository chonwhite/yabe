# Yabe Specification

### Size Encoding Format

#### Format Overview:

The size encoding format efficiently represents sizes, including array and string sizes, as well as other unsigned numbers. It prioritizes smaller sizes commonly encountered in real-world applications to optimize storage space. The format utilizes variable-length encoding, using fewer bytes for smaller sizes while still accommodating larger sizes when needed. By striking a balance between space optimization and scalability, it ensures efficient storage and transmission of size information for various applications.

The size encoding format utilizes a variable number of bytes to represent the size. The number of bytes used depends on the magnitude of the size being encoded. The format follows a scheme where the size is represented in one, two, three, or four bytes.

#### Encoding Rules:

The encoding of the size follows the following rules:

- If the size value is less than 248, it is represented in a single byte. The size value is stored directly in the byte.
- If the size value is 248 or larger and less than 503, it is represented in two bytes. The first byte is set to 249, and the second byte stores the size value minus 248.
- If the size value is 503 or larger and less than 65536, it is represented in three bytes. The first byte is set to 250, and the following two bytes store the size value minus 503.
- If the size value is 65536 or larger and less than 4294967296, it is represented in four bytes. The first byte is set to 251, and the following four bytes store the size value minus 65536.

#### Example:

Let's consider a few examples to illustrate the size encoding format:

1. If the size value is 150, it is less than 248, so it can be represented in a single byte. The binary representation of 150 is 10010110. Therefore, the size encoding for 150 would be: 10010110.

2. If the size value is 350, it is larger than 248 and less than 503, so it is represented in two bytes. The first byte is set to 249, and the second byte stores the value 350 - 248 = 102. The binary representation of 102 is 01100110. Therefore, the size encoding for 350 would be: 11111001 01100110.

3. If the size value is 1200, it is larger than 503 and less than 65536, so it is represented in three bytes. The first byte is set to 250, and the following two bytes store the value 1200 - 503 = 697. The binary representation of 697 is 10 10101001. Therefore, the size encoding for 1200 would be: 11111010 10 10101001.

4. If the size value is 100000, it is larger than 65536 and less than 4294967296, so it is represented in four bytes. The first byte is set to 251, and the following four bytes store the value 100000 - 65536 = 34464. The binary representation of 34464 is 100001100100000. Therefore, the size encoding for 100000 would be: 11111011 10000110 01000000.

#### Usage:

To utilize the size encoding format, the decoding process involves examining the bytes and reconstructing the original size value. If the first byte is less than 248, it represents the size directly. If the first byte is 249, 250, or 251, the subsequent bytes store the size value minus a predefined offset.

Please note that this size encoding format can accommodate sizes up to 4294967295 (4 bytes). If you require support for larger sizes, you may need to extend the encoding scheme accordingly.

Certainly! Here's a combined overview of the array encoding format, incorporating the previous explanations:

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
