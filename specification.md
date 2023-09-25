# Yabe Specification

### Size Encoding Format

#### Format Overview:

The size encoding format efficiently represents sizes, including array and string sizes, as well as other unsigned numbers. It prioritizes smaller sizes commonly encountered in real-world applications to optimize storage space. The format utilizes variable-length encoding, using fewer bytes for smaller sizes while still accommodating larger sizes when needed. By striking a balance between space optimization and scalability, it ensures efficient storage and transmission of size information for various applications.

The size encoding format utilizes a variable number of bytes to represent the size. The number of bytes used depends on the magnitude of the size being encoded. The format follows a scheme where the size is represented in one - nine bytes.

#### Encoding Rules:

The encoding of the size follows the following rules:
Encoding Rules:

- If the size value is less than 248, it can be represented in a single byte.
- If the size value is greater than or equal to 248 and less than 65536, it is represented in three bytes. The first byte is set to 248, and the following byte stores the value in little-endian order.
- If the size value is greater than or equal to 65536 and less than 4294967296, it is represented in five bytes. The first byte is set to 249, and the following three bytes store the value in little-endian order.
- If the size value is greater than or equal to 4294967296, it is represented in nine bytes. The first byte is set to 250, and the following eight bytes store the value in little-endian order.

Example 1: Encoding a size value of 150
- Since 150 is less than 248, it can be represented in a single byte: 0x96.
- Single byte: 0x96
- Encoding: 0x96

Example 2: Encoding a size value of 350
- Since 350 falls into the range greater than or equal to 248 and less than 65536, apply the Rule 2.
- First byte: 0xF8 (248)
- Hex Encoding: 0x015E (350 in little-endian order)
- Encoding: 0xF8 0x5E 0x01

Example 3: Encoding a size value of 26797236
- Since 26797236 is greater than or equal to 65536 and less than 4294967296, apply Rule 3.
- First byte: 0xF9 (249)
- The following four bytes represent the value 26797236 in little-endian order: 0x04 0x07 0x61 0x01.
- Encoding: 0xF9 0x04 0x07 0x61 0x01

Example 4: Encoding a size value of 8294967230
- Since 8294967230 is greater than or equal to 4294967296, we apply Rule 4.
- First byte: 0xFA (250)
- The following eight bytes represent the value 8294967230 in little-endian order: 0xCE 0x0F 0x8B 0x91 0x4E 0x00 0x00 0x00.
- Encoding: 0xFA 0xCE 0x0F 0x8B 0x91 0x4E 0x00 0x00 0x00

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

Apologies for the confusion in the previous response. Here's the corrected description of the map encoding format for null, true, and false values:

### IndexedMap Encoding 

The IndexedMap encoding format efficiently represents key-value maps by utilizing the array encoding format. It follows the following rules:

To encode a map object, follow these steps:

1. Sort the keys using the Unicode Collation Algorithm (UCA) and eliminate any duplicate keys.
2. Create a key array that contains the sorted and unique keys.
3. Create a value array that contains the corresponding values for each key.
4. Encode the map object as an array using the array encoding format:
   - For each key-value pair, key index and the value index.
   - The key index is the index of the key in the key array.
   - The value index is the index of the value in the value array.

#### Encoding Rule for Null, True, and False Values

- Null Value: If a value is null, it is represented by the value index of 0.
- True Value: If a value is true, it is represented by the value index of 1.
- False Value: If a value is false, it is represented by the value index of 2.

#### Example

Consider the map object:

```
{
  "name": "John",
  "age": 30,
  "city": "New York"
}
```

To encode this map object , we follow these steps:

1. Sorting Keys: We sort the keys of the map object using a specified sorting algorithm. In this example, the sorted keys are: "age", "city", "name".

2. Key Array: We create a key array that contains the sorted keys. In this case, the key array would be: ["age", "city", "name"].

3. Value Array: We create a value array that contains the corresponding values for each key. In this example, the grouped value array would be: ["John", "New York", 30]. The values of the same type are grouped together.

4. Encoding Key-Value Pairs: Using the key array and value array, we encode the key-value pairs as an array. Each element represents a key-value pair, where the first element is the key index and the second element is the value index.

Using the key array and grouped value array from the example, the encoded representation would be:

```
[
  {0, 2},  // Key index 0 ("age") and value index 2 ("John")
  {1, 3},  // Key index 1 ("city") and value index 3 ("New York")
  {2, 4}   // Key index 2 ("name") and value index 4 (30)
]
```

### Schema

Suppose we have a class called `Person` with the following properties:

```java
class Person {
   String name;
   int age;
   String address;
}
```


To map this `Person` class into a schema structure, we can define the following components:

1. **Keys**: We can consider the properties (`name`, `age`, and `address`) as the keys of the map. So, the `Keys` component will be `["address", "age", "name"]`.

2. **IndexedMap**: In the `IndexedMap` component, we define the key-value pairs using indices. For example, we can assign index 3 to `name`, index 4 to `address` and index 5 to `age`. So, the `KeyValuePairs` component of `IndexedMap` will be:

   ````json
   KeyValuePairs: [
     {KeyIndex: 2, ValueIndex: 3},
     {KeyIndex: 1, ValueIndex: 5},
     {KeyIndex: 0, ValueIndex: 4}
   ]
   ```

3. **TypeName**: The `TypeName` component represents the type of the map. In this case, we can set it as `"Person"`.


Let's say we have a list of `Person` objects, and each object has properties like `name`, `age`, and `address`. To make the data representation more efficient, we can use a schema.

Here's an example schema for the `Person` class:

```json
Schema:
{
   "Keys": ["address", "age", "name"],
   "Values": [$name, $address, $age],
   "IndexedMap": {
      "KeyValuePairs": [
         {"KeyIndex": 2, "ValueIndex": 3}, // value index start from 3;
         {"KeyIndex": 1, "ValueIndex": 5}, // ints goes after strings;
         {"KeyIndex": 0, "ValueIndex": 4} 
      ]
   },
   "TypeName": "Person"
}
```

Now, let's consider a list of `Person` objects:

```json
List of Persons:
[
   {"name": "John", "age": 30, "address": "123 Main St"},
   {"name": "Sarah", "age": 25, "address": "456 Elm St"}
]
```

Using the schema, we can encode the objects as follows:

```json
Encoded List:
[
   {"John", "123 Main St", 30},
   {"Sarah", "456 Elm St", 25}
]
```

In the encoded list, instead of repeating the keys for each object, we use the indices from the schema to represent the properties. For example, `3` represents the value of `"name"`, `5` represents the value of `"age"`, and `4` represents the value of `"address"`.

By referencing the schema and using indices, we eliminate the need to repeat the keys for each object, resulting in a more compact representation of the data. This approach is particularly useful when dealing with large lists of objects with similar structures, as it reduces redundancy and improves efficiency in terms of storage and processing.

