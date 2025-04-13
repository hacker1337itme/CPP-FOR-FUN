Creating a pure C++ library with inline assembly code involves several steps. I'll guide you through creating a small library from scratch, including a header file and the implementation with inline assembly. The example will showcase a simple library that performs addition and subtraction using assembly.

### Step 1: Create the Header File

Create a file named `mylib.h` for your library.

```cpp
// mylib.h
#ifndef MYLIB_H
#define MYLIB_H

class MyLib {
public:
    // Function to add two integers
    static int add(int a, int b);

    // Function to subtract two integers
    static int subtract(int a, int b);
};

#endif // MYLIB_H
```

### Step 2: Create the Implementation File

Create an implementation file named `mylib.cpp` that contains the actual code with inline assembly.

```cpp
// mylib.cpp
#include "mylib.h"

int MyLib::add(int a, int b) {
    int result;
    // Inline assembly for addition
    __asm__ (
        "addl %[val1], %[val2];"
        : [val2] "+r"(b)   // Output and input
        : [val1] "r"(a)    // Input
    );
    result = b;          // Result is stored in b after addition
    return result;
}

int MyLib::subtract(int a, int b) {
    int result;
    // Inline assembly for subtraction
    __asm__ (
        "subl %[val1], %[val2];"
        : [val2] "+r"(b)   // Output and input
        : [val1] "r"(a)    // Input
    );
    result = b;          // Result is stored in b after subtraction
    return result;
}
```

### Step 3: Compile the Library

To create a static library (libmylib.a) from the `mylib.cpp`, use the following command:

```bash
g++ -c mylib.cpp -o mylib.o
ar rcs libmylib.a mylib.o
```

### Step 4: Create a Main Program to Test the Library

Create a `main.cpp` file to test the functions from your library.

```cpp
// main.cpp
#include <iostream>
#include "mylib.h"

int main() {
    int a = 10, b = 5;

    int sum = MyLib::add(a, b);
    int diff = MyLib::subtract(a, b);

    std::cout << "Sum: " << sum << std::endl;          // Output: 15
    std::cout << "Difference: " << diff << std::endl; // Output: 5

    return 0;
}
```

### Step 5: Compile and Link the Main Program with the Library

Compile the main program and link it to the library you created:

```bash
g++ main.cpp -L. -lmylib -o my_program
```

### Step 6: Run the Program

Now you can run your program:

```bash
./my_program
```


