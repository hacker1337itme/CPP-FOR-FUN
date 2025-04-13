Creating a simple `.zip` file in C++ from scratch requires an understanding of the ZIP file format and typically some libraries to facilitate the file compression. However, if you're looking to create a "pure" implementation without any external libraries, this can be quite complex due to the ZIP specification's intricacies (like compression methods, data structure, error handling, etc.).

Nevertheless, I can give you a basic example of how to create a simple ZIP file using a minimalistic approach, focusing on just creating an uncompressed ZIP file. For a real-world application, you would likely want to use a library like `zlib` or a higher-level library like `libzip` for proper compression and features.

### Basic ZIP File Structure Overview
A ZIP file typically includes:
1. A local file header for each file.
2. The file data.
3. A central directory at the end of the file.

### Simple ZIP Implementation Steps
Here’s a minimal example to create a ZIP file containing a single uncompressed file using C++.

1. **Create a Header File (`ZipFile.h`)**:

```cpp
#ifndef ZIPFILE_H
#define ZIPFILE_H

#include <string>
#include <fstream>

class ZipFile {
public:
    ZipFile(const std::string &filename);
    ~ZipFile();
    void addFile(const std::string &fileName, const std::string &fileContent);
    void close();

private:
    void writeLocalHeader(const std::string &fileName);
    void writeData(const std::string &data);
    void writeCentralDirectory(const std::string &fileName, std::streampos filePos, size_t fileSize);

    std::ofstream zipStream;
    std::streampos centralDirectoryStart;
};

#endif // ZIPFILE_H
```

2. **Implement the ZIP functionality (`ZipFile.cpp`)**:

```cpp
#include "ZipFile.h"
#include <cstring>
#include <iostream>

ZipFile::ZipFile(const std::string &filename) {
    zipStream.open(filename, std::ios::binary);
    if (!zipStream) {
        std::cerr << "Could not open zip file for writing: " << filename << std::endl;
    }
    centralDirectoryStart = zipStream.tellp();
}

ZipFile::~ZipFile() {
    if (zipStream.is_open()) {
        close();
    }
}

void ZipFile::addFile(const std::string &fileName, const std::string &fileContent) {
    // Write local file header
    writeLocalHeader(fileName);
    // Write file data
    writeData(fileContent);
    
    // Write central directory entry at the end
    std::streampos filePos = zipStream.tellp();
    writeCentralDirectory(fileName, filePos, fileContent.size());
}

void ZipFile::writeLocalHeader(const std::string &fileName) {
    // Local file header signature
    zipStream.write("\x50\x4b\x03\x04", 4);
    // Version needed to extract (version 20, for example)
    zipStream.write("\x14\x00", 2);
    // General purpose bit flag
    zipStream.write("\x00\x00", 2);
    // Compression method (0 = no compression)
    zipStream.write("\x00\x00", 2);
    // Last mod file time and date (just zero for simplicity)
    zipStream.write("\x00\x00\x00\x00", 4);
    // CRC-32 (just zero here)
    zipStream.write("\x00\x00\x00\x00", 4);
    
    // File name length
    uint16_t nameLen = static_cast<uint16_t>(fileName.size());
    zipStream.write(reinterpret_cast<const char*>(&nameLen), 2);
    
    // Extra field length
    uint16_t extraLen = 0;
    zipStream.write(reinterpret_cast<const char*>(&extraLen), 2);
    
    // File name
    zipStream.write(fileName.c_str(), nameLen);
}

void ZipFile::writeData(const std::string &data) {
    zipStream.write(data.c_str(), data.size());
}

void ZipFile::writeCentralDirectory(const std::string &fileName, std::streampos filePos, size_t fileSize) {
    // Placeholder for the central directory entry
    zipStream.seekp(centralDirectoryStart);
    
    // Central file header signature
    zipStream.write("\x50\x4b\x01\x02", 4);
    // Version made by
    zipStream.write("\x14\x00", 2);
    // Version needed to extract
    zipStream.write("\x14\x00", 2);
    // General purpose bit flag
    zipStream.write("\x00\x00", 2);
    // Compression method
    zipStream.write("\x00\x00", 2);
    // Last mod file time
    zipStream.write("\x00\x00", 2);
    // Last mod file date
    zipStream.write("\x00\x00", 2);
    // CRC-32
    zipStream.write("\x00\x00\x00\x00", 4);
    // Compressed size
    zipStream.write("\x00\x00\x00\x00", 4);
    // Uncompressed size
    uint32_t uncompressedSize = static_cast<uint32_t>(fileSize);
    zipStream.write(reinterpret_cast<const char*>(&uncompressedSize), 4);
    
    // File name length
    uint16_t nameLen = static_cast<uint16_t>(fileName.size());
    zipStream.write(reinterpret_cast<const char*>(&nameLen), 2);
    
    // Extra field length
    uint16_t extraLen = 0;
    zipStream.write(reinterpret_cast<const char*>(&extraLen), 2);
    
    // File name
    zipStream.write(fileName.c_str(), nameLen);
    
    // End of central directory
    zipStream.seekp(0, std::ios::end);
    uint32_t centralDirectorySize = static_cast<uint32_t>(zipStream.tellp() - centralDirectoryStart);
    zipStream.write("\x50\x4b\x05\x06", 4); // End of central directory signature
    zipStream.write("\x00\x00", 2); // This disk number
    zipStream.write("\x00\x00", 2); // Start disk number
    zipStream.write(reinterpret_cast<const char*>(&centralDirectorySize), 4); // Central directory size
    zipStream.write(reinterpret_cast<const char*>(&centralDirectoryStart), 4); // Offset of central directory
    zipStream.write("\x00\x00", 2); // Comment length
}

void ZipFile::close() {
    zipStream.close();
}
```

3. **Usage Example (`main.cpp`)**:

```cpp
#include "ZipFile.h"

int main() {
    // Create a ZipFile object
    ZipFile zip("example.zip");
    
    // Add files to the zip
    zip.addFile("hello.txt", "Hello, ZIP!");
    zip.addFile("world.txt", "Goodbye, world!");
    
    // Close the zip file
    zip.close();
    
    return 0;
}
```

### Compiling
Compile the code with a C++ compiler:
```bash
g++ main.cpp ZipFile.cpp -o create_zip
```

### Important Notes
1. **Limitations**: The above implementation does not handle compression, error handling, or various ZIP features. It simply creates an uncompressed ZIP file with flat file structure.
2. **Real-world usage**: For real compression (e.g., using Deflate algorithm, which is typical in ZIP files), using a library such as `zlib` is advisable.
3. **End of the Central Directory**: Would need to compute the actual ranges for each entry properly.
4. **Cross-platform**: The code should work on both Windows and Linux/Unix-like systems, but ensure that file paths are handled correctly.

