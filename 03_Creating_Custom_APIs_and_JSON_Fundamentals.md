# 03 Creating Custom APIs and JSON Fundamentals

## Why Create Custom APIs?

Instead of relying on external demo APIs that may be:
- Inconsistent or unreliable
- Limited in functionality
- Sometimes unavailable

We'll create our own APIs for:
- **Consistent testing environment**
- **Complete control over data**
- **Learning API structure**
- **Practice without dependencies**

## Prerequisites Installation

### Step 1: Install Node.js

**What you get with Node.js:**
- Node.js runtime environment
- **npm** (Node Package Manager) - comes automatically
- Foundation for running JavaScript servers

**Download Process:**
1. Go to Google → Search "download node.js"
2. Visit official website: `https://nodejs.org`
3. Choose your version:
   - **Windows**: Select Windows Installer (64-bit recommended)
   - **Mac**: Select macOS Installer (.pkg file)

**Installation Steps:**
1. Download the installer (.msi for Windows, .pkg for Mac)
2. Double-click the installer
3. Follow installation wizard:
   - Accept license agreement
   - Use default installation location
   - Keep default components selected
   - **Uncheck** additional tools (Python, Visual Studio) if not needed
4. Click Install and wait for completion

### Step 2: Verify Installation

Open Command Prompt (Windows) or Terminal (Mac) and run:

```bash
# Check Node.js version
node --version

# Check npm version
npm --version
```

**Expected Output:**
```
node --version
v18.17.0

npm --version
9.6.7
```

### Step 3: Install JSON Server

JSON Server creates a REST API from a JSON file.

```bash
npm install -g json-server
```

**What this does:**
- `-g` flag installs globally (accessible from anywhere)
- Creates a tool to convert JSON files into REST APIs
- Provides GET, POST, PUT, DELETE endpoints automatically

## Creating Your First API

### Step 1: Create JSON Data File

Create a file named `students.json` with the following content:

```json
{
  "students": [
    {
      "id": 1,
      "name": "John Doe",
      "location": "New York",
      "phone": "1234567890",
      "courses": ["JavaScript", "Python"]
    },
    {
      "id": 2,
      "name": "Jane Smith", 
      "location": "California",
      "phone": "0987654321",
      "courses": ["Java", "C++"]
    },
    {
      "id": 3,
      "name": "Mike Johnson",
      "location": "Texas", 
      "phone": "1122334455",
      "courses": ["Python", "React"]
    }
  ]
}
```

### Step 2: Start the API Server

1. Open Command Prompt/Terminal
2. Navigate to the folder containing `students.json`
3. Run the command:

```bash
json-server students.json
```

**Expected Output:**
```
\{^_^}/ hi!

Loading students.json
Done

Resources
http://localhost:3000/students

Home
http://localhost:3000
```

### Step 3: Test Your API

Open browser and visit: `http://localhost:3000/students`

You should see your JSON data displayed as a web API response.

**Important Notes:**
- Keep the command window open - closing it stops the API
- API runs on `localhost:3000` by default
- The API automatically creates REST endpoints based on your JSON structure

## API Endpoints Reference

Based on our `students.json` file, JSON Server automatically creates these endpoints:

| Method | URL | Purpose | Request Body | Response |
|--------|-----|---------|--------------|----------|
| **GET** | `/students` | Get all students | None | Array of all students |
| **GET** | `/students/1` | Get student by ID | None | Single student object |
| **POST** | `/students` | Create new student | Student data (no ID) | Created student with auto-generated ID |
| **PUT** | `/students/1` | Update student completely | Complete student data | Updated student object |
| **DELETE** | `/students/1` | Delete student | None | Empty response (204) |

### Example API Requests

**Get All Students:**
```
GET http://localhost:3000/students
Response: Array of all student objects
Status: 200 OK
```

**Get Single Student:**
```
GET http://localhost:3000/students/1
Response: Single student object with ID 1
Status: 200 OK
```

**Create New Student:**
```
POST http://localhost:3000/students
Body: {
  "name": "Alice Brown",
  "location": "Florida", 
  "phone": "5566778899",
  "courses": ["C#", "Angular"]
}
Response: Created student with auto-generated ID
Status: 201 Created
```

**Update Student:**
```
PUT http://localhost:3000/students/1
Body: {
  "name": "John Updated",
  "location": "Updated Location",
  "phone": "9999999999", 
  "courses": ["Updated Course"]
}
Response: Updated student object
Status: 200 OK
```

**Delete Student:**
```
DELETE http://localhost:3000/students/1
Response: Empty
Status: 204 No Content
```

## Testing Custom API in Postman

### Create New Collection

1. Open Postman
2. Create new collection: "Student API Tests"
3. Set base URL: `http://localhost:3000`

### Test Cases to Create

**Test Case 1: Get All Students**
- Method: GET
- URL: `http://localhost:3000/students`
- Expected: Array of 3 students
- Status Code: 200

**Test Case 2: Get Single Student**
- Method: GET  
- URL: `http://localhost:3000/students/1`
- Expected: Student object with ID 1
- Status Code: 200

**Test Case 3: Create New Student**
- Method: POST
- URL: `http://localhost:3000/students`
- Body (JSON):
```json
{
  "name": "Test Student",
  "location": "Test Location",
  "phone": "1111111111",
  "courses": ["Test Course"]
}
```
- Expected: Created student with auto-generated ID
- Status Code: 201

**Test Case 4: Update Student**
- Method: PUT
- URL: `http://localhost:3000/students/4` (use ID from created student)
- Body (JSON):
```json
{
  "name": "Updated Student",
  "location": "Updated Location", 
  "phone": "2222222222",
  "courses": ["Updated Course"]
}
```
- Expected: Updated student object
- Status Code: 200

**Test Case 5: Delete Student**
- Method: DELETE
- URL: `http://localhost:3000/students/4`
- Expected: Empty response
- Status Code: 204

## JSON Fundamentals

### What is JSON?

**JSON** = **J**ava**S**cript **O**bject **N**otation

**Key Characteristics:**
- **Data format** for storing and exchanging data
- **Human-readable** and easy to understand
- **Lightweight** for network transmission
- **Language-independent** (despite the name)
- **Derived from JavaScript** syntax

### Why Use JSON?

**Advantages over other formats:**
- **Lightweight**: Faster data transmission
- **Secure**: Supports encryption/decryption
- **Universal**: Supported by all modern programming languages
- **Simple**: Easy to read and write
- **Structured**: Organized key-value pairs

**JSON vs XML:**
| Feature | JSON | XML |
|---------|------|-----|
| **Readability** | High | Medium |
| **File Size** | Smaller | Larger |
| **Parsing Speed** | Faster | Slower |
| **Array Support** | Native | Complex |
| **Data Types** | Limited but sufficient | Extensive |

### JSON Data Types

| Type | Description | Example |
|------|-------------|---------|
| **String** | Text in double quotes | `"John Doe"` |
| **Number** | Integer or decimal | `25`, `3.14` |
| **Boolean** | True or false | `true`, `false` |
| **null** | Unknown/empty value | `null` |
| **Object** | Key-value pairs in `{}` | `{"name": "John"}` |
| **Array** | List of values in `[]` | `[1, 2, 3]` |

### JSON Syntax Rules

1. **Data in key-value pairs**: `"key": "value"`
2. **Keys must be strings**: Always in double quotes
3. **Values follow data type rules**: Strings in quotes, numbers without
4. **Comma separation**: Between key-value pairs
5. **Curly braces**: `{}` for objects
6. **Square brackets**: `[]` for arrays
7. **No trailing commas**: Last item has no comma

### JSON Structure Examples

**Simple Object:**
```json
{
  "name": "John Doe",
  "age": 30,
  "active": true,
  "address": null
}
```

**Object with Array:**
```json
{
  "name": "John Doe",
  "phoneNumbers": [
    "123-456-7890",
    "098-765-4321"
  ],
  "married": false
}
```

**Array of Objects:**
```json
{
  "students": [
    {
      "id": 1,
      "name": "John",
      "grade": "A"
    },
    {
      "id": 2, 
      "name": "Jane",
      "grade": "B"
    }
  ]
}
```

**Complex Nested Structure:**
```json
{
  "company": "Tech Corp",
  "employees": [
    {
      "id": 101,
      "name": "John Doe",
      "department": {
        "name": "Engineering",
        "location": "Building A"
      },
      "skills": ["JavaScript", "Python", "React"],
      "active": true
    }
  ]
}
```

## JSON Path Fundamentals

### What is JSON Path?

JSON Path is a query language for JSON, similar to XPath for XML. It allows you to:
- **Extract specific data** from JSON structures
- **Navigate complex nested objects**
- **Access array elements by index**
- **Validate response data** in API testing

### JSON Path Syntax

| Symbol | Description | Example |
|--------|-------------|---------|
| `$` | Root element | `$` |
| `.` | Child element | `$.name` |
| `[]` | Array index | `$[0]` |
| `*` | Wildcard | `$.*` |

### JSON Path Examples

Given this JSON:
```json
{
  "students": [
    {
      "id": 1,
      "name": "John",
      "courses": ["Math", "Science"]
    },
    {
      "id": 2,
      "name": "Jane", 
      "courses": ["English", "History"]
    }
  ]
}
```

**JSON Path Examples:**
- `$.students` → Returns entire students array
- `$.students[0]` → Returns first student object
- `$.students[0].name` → Returns "John"
- `$.students[1].id` → Returns 2
- `$.students[0].courses[1]` → Returns "Science"

### JSON Path Tools

**1. JSON Path Finder** (`jsonpathfinder.com`)
- **Purpose**: Automatically generate JSON paths
- **How to use**:
  1. Paste your JSON data
  2. Click on any field in the visual tree
  3. Get the JSON path automatically generated

**2. JSON Path Evaluator** (`jsonpath.com`)
- **Purpose**: Test and validate JSON paths
- **How to use**:
  1. Paste your JSON data
  2. Enter your JSON path expression
  3. See the extracted value

### Practical JSON Path Exercise

Using our student data, write JSON paths for:

1. **Get all students**: `$.students`
2. **Get first student's name**: `$.students[0].name`
3. **Get second student's phone**: `$.students[1].phone`
4. **Get first course of third student**: `$.students[2].courses[0]`

## Response Validation Areas

When testing APIs, validate these response components:

### 1. Status Code Validation
- **2xx**: Success (200, 201, 204)
- **4xx**: Client errors (400, 401, 404)
- **5xx**: Server errors (500, 502, 503)

### 2. Response Body Validation
- **Data accuracy**: Correct values returned
- **Data structure**: Proper JSON format
- **Required fields**: All expected fields present
- **Data types**: Correct types (string, number, boolean)

### 3. Response Headers Validation
- **Content-Type**: `application/json`
- **Response time**: Within acceptable limits
- **Custom headers**: API-specific headers

### 4. Response Metrics
- **Response time**: Performance validation
- **Response size**: Data volume check
- **Cookies**: Session management validation

## Best Practices

### API Development
1. **Use meaningful resource names** (`/students` not `/data`)
2. **Follow REST conventions** (GET for read, POST for create)
3. **Include proper status codes** in responses
4. **Structure JSON consistently** across endpoints

### Testing Strategy
1. **Test happy path first** (successful scenarios)
2. **Test edge cases** (invalid IDs, missing data)
3. **Validate all response components** (status, body, headers)
4. **Use descriptive test names** for clarity

### JSON Best Practices
1. **Use consistent naming** (camelCase or snake_case)
2. **Keep structure simple** when possible
3. **Validate JSON syntax** before testing
4. **Document your JSON structure** for team members

## Common Issues and Solutions

### API Server Issues
**Problem**: API not starting
**Solution**: Check if port 3000 is available, try different port with `json-server --port 3001 students.json`

**Problem**: Changes not reflected
**Solution**: Restart JSON server after modifying JSON file

### JSON Issues
**Problem**: Invalid JSON syntax
**Solution**: Use JSON validator tools, check for missing commas/quotes

**Problem**: Cannot extract data with JSON path
**Solution**: Verify JSON structure, use JSON path testing tools

## Next Steps

In the next module, we will cover:
1. **Advanced Postman validation techniques**
2. **Writing automated test scripts**
3. **Environment variables and data management**
4. **Collection runners and automation**
5. **Integration with CI/CD pipelines**

## Practice Exercises

### Exercise 1: Create Your Own API
1. Create a `products.json` file with product data
2. Start JSON server with your file
3. Test all CRUD operations in Postman

### Exercise 2: JSON Path Practice
1. Create complex nested JSON structure
2. Write JSON paths to extract specific values
3. Validate paths using online tools

### Exercise 3: API Testing
1. Create comprehensive test collection
2. Test all endpoints with different scenarios
3. Validate responses thoroughly

Remember: Understanding JSON and JSON Path is crucial for effective API testing. Practice these concepts thoroughly before moving to advanced validation techniques.