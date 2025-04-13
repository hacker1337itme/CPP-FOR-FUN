Creating a pure C++ library for handling notifications can be a fun project. Below, I will outline a simple example that demonstrates how to create a C++ notification library from scratch, including the necessary class declarations in a header file. This example will focus on a console-based notification system to keep it simple and portable.

### Step 1: Define the Header File

Create a header file named `Notification.h`. This file will contain the declaration of the `Notification` class and its methods.

```cpp
// Notification.h

#ifndef NOTIFICATION_H
#define NOTIFICATION_H

#include <string>

class Notification
{
public:
    // Constructor
    Notification(const std::string& title, const std::string& message);

    // Method to display the notification
    void display() const;

private:
    std::string title_;
    std::string message_;
};

#endif // NOTIFICATION_H
```

### Step 2: Implement the Notification Class

Create a source file named `Notification.cpp` to implement the `Notification` class.

```cpp
// Notification.cpp

#include "Notification.h"
#include <iostream>

// Constructor implementation
Notification::Notification(const std::string& title, const std::string& message)
    : title_(title), message_(message) {}

// Display method implementation
void Notification::display() const {
    std::cout << "Notification: " << title_ << std::endl;
    std::cout << "Message: " << message_ << std::endl;
}
```

### Step 3: Create a Simple Test Application

Create a test application to use this notification library. For this, create a file named `main.cpp`.

```cpp
// main.cpp

#include "Notification.h"

int main() {
    // Create a notification
    Notification notify("Sample Notification", "This is a test message.");
    
    // Display the notification
    notify.display();
    
    return 0;
}
```

### Step 4: Compile the Library and Test Application

Now, you can compile the library and the test application using a C++ compiler. Here's how you can do it with `g++`:

```bash
g++ -c Notification.cpp        // Compile the Notification class
g++ -c main.cpp                // Compile the test application
g++ -o notify_app Notification.o main.o   // Link both objects to create the executable
```

### Step 5: Run the Application

After compiling, you can run the application:

```bash
./notify_app
```

### Output

When you run your application, you should see output like this:

```
Notification: Sample Notification
Message: This is a test message.
```

### Summary

In this example, you've created a simple C++ notification library with a header file and a source file, followed by a test application to use the library. This is just a basic implementation; you could expand upon it by adding features such as different notification types, formatting options, and even integrating with GUI libraries or system notification services depending on your needs.
