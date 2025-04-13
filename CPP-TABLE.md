Creating a command-line interface (CLI) application in C++ from scratch can be a great way to explore C++ features and build a simple table representation. I will guide you through the process of creating a simple library for handling tabular data, which can be used from a command-line interface.

### Steps to Create a Simple Table CLI in C++

1. **Define the Table Class in Header File** (`Table.h`)
2. **Implement the Table Class in Source File** (`Table.cpp`)
3. **Create a CLI Application** (`main.cpp`)
4. **Compile and Run the Application**

### 1. Define the Table Class in Header File (`Table.h`)

```cpp
#ifndef TABLE_H
#define TABLE_H

#include <vector>
#include <string>
#include <iostream>
#include <iomanip>

class Table {
public:
    // Constructor
    Table(const std::vector<std::string>& columnNames);

    // Add a row of data to the table
    void addRow(const std::vector<std::string>& row);

    // Display the table
    void display() const;

private:
    std::vector<std::string> columnNames;
    std::vector<std::vector<std::string>> rows;

    // Helper function to get the maximum width of the columns
    std::vector<size_t> getColumnWidths() const;
};

#endif // TABLE_H
```

### 2. Implement the Table Class in Source File (`Table.cpp`)

```cpp
#include "Table.h"

// Constructor
Table::Table(const std::vector<std::string>& columnNames) : columnNames(columnNames) {}

// Add a row of data to the table
void Table::addRow(const std::vector<std::string>& row) {
    rows.push_back(row);
}

// Helper function to get the maximum width of the columns
std::vector<size_t> Table::getColumnWidths() const {
    std::vector<size_t> widths(columnNames.size());

    // Calculate max width for each column
    for (size_t i = 0; i < columnNames.size(); ++i) {
        widths[i] = columnNames[i].length();
        for (const auto& row : rows) {
            if (i < row.size()) {
                widths[i] = std::max(widths[i], row[i].length());
            }
        }
    }
    return widths;
}

// Display the table
void Table::display() const {
    std::vector<size_t> widths = getColumnWidths();

    // Print header
    for (size_t i = 0; i < columnNames.size(); ++i) {
        std::cout << std::left << std::setw(widths[i]) << columnNames[i];
    }
    std::cout << std::endl;

    // Print separator
    for (size_t width : widths) {
        std::cout << std::string(width, '-') << " ";
    }
    std::cout << std::endl;

    // Print rows
    for (const auto& row : rows) {
        for (size_t i = 0; i < columnNames.size(); ++i) {
            if (i < row.size()) {
                std::cout << std::left << std::setw(widths[i]) << row[i];
            } else {
                std::cout << std::left << std::setw(widths[i]) << "";
            }
        }
        std::cout << std::endl;
    }
}
```

### 3. Create a CLI Application (`main.cpp`)

```cpp
#include <iostream>
#include "Table.h"

int main() {
    // Define column names
    std::vector<std::string> columns = {"Name", "Age", "Occupation"};

    // Create a Table object
    Table table(columns);

    // Add some rows
    table.addRow({"Alice", "30", "Engineer"});
    table.addRow({"Bob", "25", "Designer"});
    table.addRow({"Charlie", "28", "Teacher"});

    // Display the table
    table.display();

    return 0;
}
```

### 4. Compile and Run the Application

You can compile the code using g++ (or any other C++ compiler) from the terminal:

```bash
g++ -o table_cli main.cpp Table.cpp
```

After compiling, run the application:

```bash
./table_cli
```

### Output

You should see a formatted table displayed in the terminal like this:

```
Name    Age Occupation  
-----------------------
Alice   30 Engineer    
Bob     25 Designer    
Charlie 28 Teacher     
```

### Summary

This example shows how to create a simple C++ library for managing and displaying tabular data, coupled with a basic command-line interface. You can enhance this further by adding features such as loading data from files, sorting rows, or adding more complex data types.
