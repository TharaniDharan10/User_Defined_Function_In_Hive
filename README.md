# Hive Custom UDF (User Defined Function)

A custom Apache Hive User Defined Function (UDF) implementation that provides string manipulation and concatenation capabilities for Hive queries.

## Table of Contents

- [Overview](#overview)
- [Features](#features)
- [Prerequisites](#prerequisites)
- [Project Structure](#project-structure)
- [Build Instructions](#build-instructions)
- [Deployment](#deployment)
- [Usage Examples](#usage-examples)
- [Function Details](#function-details)
- [Technologies Used](#technologies-used)
- [Contributing](#contributing)
- [License](#license)

## Overview

This project implements a custom Hive UDF that extends the functionality of Apache Hive by providing custom string operations. The UDF supports method overloading, allowing it to handle different input parameters and return formatted text outputs.

## Features

- **String to Uppercase Conversion**: Converts input text to uppercase
- **ID-Name Concatenation**: Combines an integer ID with a name string
- **Method Overloading**: Supports multiple evaluate methods for different use cases
- **Null Safety**: Handles null inputs gracefully
- **Optimized Performance**: Uses reusable Text objects to minimize object creation

## Prerequisites

Before building and deploying this UDF, ensure you have:

- Java Development Kit (JDK) 8 or higher
- Apache Maven 3.6+
- Apache Hadoop 3.2.1+
- Apache Hive 3.1.2+
- IntelliJ IDEA (or any Java IDE)

## Project Structure

```
hive-udf/
├── src/
│   ├── main/
│   │   └── java/
│   │       └── com/
│   │           └── example/
│   │               └── UDF/
│   │                   └── HiveUDF.java
│   └── test/
│       └── java/
├── pom.xml
└── README.md
```

## Build Instructions

### Step 1: Clone the Repository

```bash
git clone https://github.com/TharaniDharan10/User_Defined_Function_In_Hive
cd User_Defined_Function_In_Hive
```

### Step 2: Build the Project

Use Maven to clean and build the project:

```bash
mvn clean install
```

This will generate `UDF_Jar.jar` in the `target/` directory.

### Step 3: Locate the JAR

After successful build, find your JAR file at:

```
target/UDF_Jar.jar
```

## Deployment

### Step 1: Transfer JAR to Hive Environment

Since the development is done on Mac and deployment is on Ubuntu (VMware), transfer the JAR file:

**Option 1: Using SCP**
```bash
# From Mac terminal
scp target/UDF_Jar.jar username@ubuntu-vm-ip:/home/danny/
```

**Option 2: Using Shared Folder**
```bash
# Copy to shared folder accessible by VM
# Then from Ubuntu VM
cp /path/to/shared/UDF_Jar.jar /home/danny/
```

**Option 3: Manual Transfer**
- Copy the JAR from Mac's `target/` directory
- Paste it to Ubuntu VM's `/home/danny/` directory

### Step 2: Register the JAR in Hive

Open Hive CLI or Beeline and execute:

```sql
-- Add the JAR to Hive session
ADD JAR /home/danny/UDF_Jar.jar;

-- Verify JAR is loaded
LIST JARS;

-- Create temporary function
CREATE TEMPORARY FUNCTION my_function AS 'com.example.UDF.HiveUDF';
```

**Note**: The function is temporary and will be available only for the current Hive session. For permanent functions, use `CREATE FUNCTION` with a database prefix.

## Usage Examples

### Example 1: Convert String to Uppercase

```sql
SELECT my_function('Abdul Kalam');
```

**Output:**
```
ABDUL KALAM
```

### Example 2: Concatenate ID with Name

```sql
SELECT my_function(1, 'Abdul Kalam');
```

**Output:**
```
1-Abdul Kalam
```

### Example 3: Use in a Table Query

Assuming you have a table `employees`:

```sql
-- Convert employee names to uppercase
SELECT 
    emp_id,
    my_function(name) AS upper_name
FROM employees;

-- Concatenate employee ID with name
SELECT 
    my_function(emp_id, name) AS formatted_employee
FROM employees;
```

## Function Details

### Method 1: evaluate(Text Name)

**Description**: Converts the input text to uppercase.

**Parameters**:
- `Name` (Text): Input string to convert

**Returns**: Text - Uppercase version of input string, or null if input is null

**Example**:
```sql
SELECT my_function('hello world');
-- Output: HELLO WORLD
```

---

### Method 2: evaluate(IntWritable id, Text Name)

**Description**: Concatenates an integer ID with a name, separated by a hyphen.

**Parameters**:
- `id` (IntWritable): Integer ID value
- `Name` (Text): Name or text to concatenate

**Returns**: Text - Formatted string as "ID-Name", or null if any input is null

**Example**:
```sql
SELECT my_function(101, 'John Doe');
-- Output: 101-John Doe
```

## Technologies Used

- **Java 8**: Programming language
- **Apache Hive 3.1.2**: Data warehouse infrastructure
- **Apache Hadoop 3.2.1**: Distributed computing framework
- **Maven**: Build automation and dependency management
- **JUnit 5**: Testing framework (for future test implementations)

## Development Environment

- **Development OS**: macOS
- **Deployment OS**: Ubuntu (VMware)
- **IDE**: IntelliJ IDEA

## Contributing

Contributions are welcome! Please follow these steps:

1. Fork the repository
2. Create a feature branch (`git checkout -b feature/YourFeature`)
3. Commit your changes (`git commit -m 'Add some feature'`)
4. Push to the branch (`git push origin feature/YourFeature`)
5. Open a Pull Request

## Development Notes

### Adding New UDF Methods

To add new functionality:

1. Add a new `evaluate()` method to `HiveUDF.java`
2. Ensure proper null handling
3. Rebuild the project with `mvn clean install`
4. Transfer the new JAR to Ubuntu VM
5. Redeploy in Hive

### Creating Permanent Functions

To make the function permanent in Hive:

```sql
CREATE FUNCTION my_database.my_function AS 'com.example.UDF.HiveUDF'
USING JAR 'hdfs:///path/to/UDF_Jar.jar';
```

## Acknowledgments

- Apache Hive Community
- Apache Hadoop Project
- Contributors and maintainers

---

**Note**: Make sure to adjust Hive and Hadoop versions in `pom.xml` to match your cluster configuration for compatibility.
