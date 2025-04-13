Creating a simple Base64 encoding and decoding C++ library from scratch involves writing both the header file (`Base64.h`) and the implementation file (`Base64.cpp`). Below is a straightforward implementation of a Base64 library for encoding and decoding strings. This example doesn't rely on any external libraries.

### Base64.h
```cpp
#ifndef BASE64_H
#define BASE64_H

#include <string>
#include <vector>

class Base64 {
public:
    static std::string encode(const std::string &input);
    static std::string decode(const std::string &input);
    
private:
    static const char* base64_chars;
    
    static bool is_base64(unsigned char c);
};

#endif // BASE64_H
```

### Base64.cpp
```cpp
#include "Base64.h"

const char* Base64::base64_chars = 
    "ABCDEFGHIJKLMNOPQRSTUVWXYZ"
    "abcdefghijklmnopqrstuvwxyz"
    "0123456789+/";

std::string Base64::encode(const std::string &input) {
    std::string output;
    int i = 0;
    int input_len = input.length();
    
    while (input_len--) {
        if (i == 0) {
            output += base64_chars[(input[0] >> 2) & 0x3F];
            output += base64_chars[(input[0] & 0x03) << 4];
            i++;
        } else if (i == 1) {
            output[output.length() - 1] |= (input[0] >> 4) & 0x0F;
            output += base64_chars[(input[0] & 0x0F) << 2];
            i++;
        } else if (i == 2) {
            output[output.length() - 1] |= (input[0] >> 6) & 0x03;
            output += base64_chars[input[0] & 0x3F];
            i = 0;
        }
    }

    // Add padding
    while (i-- > 0) {
        output += '=';
    }

    return output;
}

std::string Base64::decode(const std::string &input) {
    int in_len = input.length();
    int i = 0;
    int j = 0;
    int index = 0;
    std::vector<unsigned char> buffer(3);
    std::string output;

    while (in_len-- && (input[index] != '=') && is_base64(input[index])) {
        buffer[i++] = input[index]; index++;
        if (i == 4) {
            for (i = 0; i < 4; i++) {
                buffer[i] = static_cast<unsigned char>(std::string(base64_chars).find(buffer[i]));
            }
            output += (buffer[0] << 2) + ((buffer[1] & 0x30) >> 4);
            output += ((buffer[1] & 0x0F) << 4) + ((buffer[2] & 0x3C) >> 2);
            output += ((buffer[2] & 0x03) << 6) + buffer[3];
            i = 0;
        }
    }

    if (i) {
        for (j = i; j < 4; j++) {
            buffer[j] = 0;
        }
        
        for (j = 0; j < 4; j++) {
            buffer[j] = static_cast<unsigned char>(std::string(base64_chars).find(buffer[j]));
        }

        output += (buffer[0] << 2) + ((buffer[1] & 0x30) >> 4);
        if (buffer[2] != 64) {
            output += ((buffer[1] & 0x0F) << 4) + ((buffer[2] & 0x3C) >> 2);
        }
        if (buffer[3] != 64) {
            output += ((buffer[2] & 0x03) << 6) + buffer[3];
        }
    }

    return output;
}

bool Base64::is_base64(unsigned char c) {
    return (isalnum(c) || (c == '+') || (c == '/'));
}
```

### Usage Example
To use this Base64 class, you can include the header file and call the encode and decode methods. Here's a simple `main.cpp` file to demonstrate its usage:

```cpp
#include <iostream>
#include "Base64.h"

int main() {
    std::string original = "Hello, World!";
    std::string encoded = Base64::encode(original);
    std::string decoded = Base64::decode(encoded);
    
    std::cout << "Original: " << original << std::endl;
    std::cout << "Encoded: " << encoded << std::endl;
    std::cout << "Decoded: " << decoded << std::endl;

    return 0;
}
```

### Compilation
To compile this example, use a command like the following (depending on your compiler):

```sh
g++ -o base64_example Base64.cpp main.cpp
```


