# YABE (Yet Another Binary Encoding)

YABE is a binary encoding format designed to address the limitations of text-based formats like JSON when it comes to encoding binary data. While JSON is widely used and offers flexibility for representing structured data, it is inefficient for encoding binary data due to its reliance on text-based representations. YABE aims to provide a solution that combines the flexibility of JSON with the ability to encode binary data efficiently, while also offering fast decoding and compactness.

## Key Features

YABE offers several key features that make it a valuable encoding format:

1. **Efficient Encoding**: YABE excels at efficiently encoding binary data. It provides a compact representation that reduces the storage space required, making it particularly useful for large volumes of binary data such as images, audio, or video files.

2. **Faster Decoding**: YABE is designed for fast decoding. Its binary representation allows for direct access and processing of data without the need for parsing or interpretation, resulting in improved decoding performance.

3. **Flexibility**: YABE aims to provide the flexibility of JSON while being able to encode binary data. It allows for the representation of structured data, arrays, objects, strings, floats, and other data types, making it versatile for various use cases.

4. **Compatibility**: YABE is designed to be compatible with existing systems and programming languages. It can be seamlessly integrated into different software environments, enabling easy adoption and interoperability.

## Encoding

In YABE (Yet Another Binary Encoding), keys are represented as strings and can be sorted for faster lookup. Duplicate keys are eliminated by referencing them with indices. Values are grouped and indexed, eliminating the need for explicit data type encoding. The object structure is stored using key and value indices, enabling fast decoding comparable to array indexing and pointer dereferencing. This approach eliminates the need for constructing a DOM object in memory, resulting in efficient processing, reduced memory usage, and a concise binary encoding format for key-value data.

## Benchmarks

TODO

## Specification

[Specification](specification.md)

## Implementations

- [C++](https://github.com/chonwhite/yabe-cpp)
- [Java](https://github.com/chonwhite/yabe-java)

## Agenda

- Converting back and forth to JSON
- Working with the Document Object Model (DOM)
- Supporting various programming languages (C++, Java, JavaScript, etc.)
- Schema definition and extraction
- Interface Definition Language (IDL)

## Contributing
We welcome all types of contributions, including but not limited to:

- Bug fixes
- Feature enhancements
- Documentation improvements
- Code optimizations
- Test coverage expansions
- Different language implementations

If you have any ideas or suggestions, please feel free to open an issue to discuss them further.
