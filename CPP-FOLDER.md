
Creating a C++ library for folder (directory) manipulation can be quite useful for tasks such as creating, deleting, listing, and navigating directories. Below is a simple C++ header file and a corresponding implementation file that provides basic functionality to manipulate folders. It uses the C++ standard library's filesystem capabilities introduced in C++17.

### FolderManipulation.h

```cpp
#ifndef FOLDER_MANIPULATION_H
#define FOLDER_MANIPULATION_H

#include <string>
#include <vector>

class FolderManipulation {
public:
    // Constructor
    FolderManipulation() = default;

    // Create a new directory
    static bool createDirectory(const std::string &path);

    // Remove a directory
    static bool removeDirectory(const std::string &path);

    // List all files and directories within a directory
    static std::vector<std::string> listDirectory(const std::string &path);

    // Check if a directory exists
    static bool directoryExists(const std::string &path);
};

#endif // FOLDER_MANIPULATION_H
```

### FolderManipulation.cpp

```cpp
#include "FolderManipulation.h"
#include <filesystem>
#include <iostream>

namespace fs = std::filesystem;

bool FolderManipulation::createDirectory(const std::string &path) {
    return fs::create_directory(path);
}

bool FolderManipulation::removeDirectory(const std::string &path) {
    return fs::remove(path);
}

std::vector<std::string> FolderManipulation::listDirectory(const std::string &path) {
    std::vector<std::string> entries;
    if (directoryExists(path)) {
        for (const auto &entry : fs::directory_iterator(path)) {
            entries.push_back(entry.path().string());
        }
    }
    return entries;
}

bool FolderManipulation::directoryExists(const std::string &path) {
    return fs::exists(path) && fs::is_directory(path);
}
```

### Example Usage

Here’s how you could use the `FolderManipulation` class in a main program:

```cpp
#include <iostream>
#include "FolderManipulation.h"

int main() {
    std::string dirPath = "test_directory";

    // Create a directory
    if (FolderManipulation::createDirectory(dirPath)) {
        std::cout << "Directory created: " << dirPath << std::endl;
    } else {
        std::cout << "Failed to create directory or it already exists." << std::endl;
    }

    // List the directory
    std::vector<std::string> entries = FolderManipulation::listDirectory(dirPath);
    std::cout << "Contents of " << dirPath << ":" << std::endl;
    for (const auto &entry : entries) {
        std::cout << entry << std::endl;
    }

    // Remove the directory
    if (FolderManipulation::removeDirectory(dirPath)) {
        std::cout << "Directory removed: " << dirPath << std::endl;
    } else {
        std::cout << "Failed to remove directory or it does not exist." << std::endl;
    }

    return 0;
}
```

### Notes

1. **C++ Standard Requirements**: This code requires a C++17 or later standard compiler for the filesystem library.

2. **Error Handling**: This example uses basic boolean returns for error handling. In a real-world application, you may want to throw exceptions or provide more detailed error messages.

3. **Cross-Platform**: The `std::filesystem` library is designed to work across different platforms, but you should test your application on each intended platform to ensure compatibility and behavior.

4. **Advanced Features**: Depending on your needs, you might want to add more features like copying files, moving directories, checking permissions, etc.
