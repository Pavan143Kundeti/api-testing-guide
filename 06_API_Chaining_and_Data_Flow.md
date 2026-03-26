# 06 API Chaining and Data Flow

## What is API Chaining?

API Chaining is the process where the response from one API request becomes the input (or part of the input) for another API request. This creates a dependency chain between multiple API calls.

### Key Concept
```
API 1 Request → API 1 Response → Extract Data → API 2 Request → API 2 Response
```

### Real-World Example
1. **Create User** → Get User ID from response
2. **Get User Details** → Use User ID to fetch user information
3. **Update User** → Use User ID to modify user data
4. **Delete User** → Use User ID to remove user

## Why Use API Chaining?

### Common Scenarios
- **User Management**: Create → Read → Update → Delete operations
- **E-commerce**: Create Order → Add Items → Calculate Total → Process Payment
- **Authentication**: Login → Get Token → Access Protected Resources
- **Data Dependencies**: Parent record creation before child records

### Benefits
- **Realistic Testing**: Mimics real application workflows
- **Data Consistency**: Ensures related operations use consistent data
- **Dynamic Testing**: Eliminates hardcoded values
- **End-to-End Validation**: Tests complete business processes

## Basic API Chaining Example (Student API)

### Step 1: Create Student (POST)
```javascript
// Pre-request Script - Generate dynamic data
const randomString = Math.random().toString(36).substring(2, 8);
const studentName = `Student_${randomString}`;
const studentEmail = `student_${randomString}@example.com`;

// Set as environment variables
pm.environment.set("studentName", studentName);
pm.environment.set("studentEmail", studentEmail);
```

**Request Body:**
```json
{
  "name": "{{studentName}}",
  "location": "New York",
  "phone": "1234567890",
  "courses": ["JavaScript", "Python"]
}
```

**Test Script - Extract ID:**
```javascript
// Extract response data
const jsonData = pm.response.json();

// Store student ID for next requests
pm.environment.set("studentId", jsonData.id);

// Validation
pm.test("Student created successfully", () => {
    pm.response.to.have.status(201);
    pm.expect(jsonData.id).to.be.a('number');
});
```

### Step 2: Get Student Details (GET)
**URL:** `http://localhost:3000/students/{{studentId}}`

**Test Script - Validate Data:**
```javascript
pm.test("Student data validation", () => {
    const jsonData = pm.response.json();
    
    // Compare with original data
    pm.expect(jsonData.id).to.equal(parseInt(pm.environment.get("studentId")));
    pm.expect(jsonData.name).to.equal(pm.environment.get("studentName"));
    pm.expect(jsonData.email).to.equal(pm.environment.get("studentEmail"));
});
```

### Step 3: Delete Student (DELETE)
**URL:** `http://localhost:3000/students/{{studentId}}`

**Test Script - Cleanup:**
```javascript
pm.test("Student deleted successfully", () => {
    pm.response.to.have.status(204);
});

// Clean up environment variables
pm.environment.unset("studentId");
pm.environment.unset("studentName");
pm.environment.unset("studentEmail");
```

## Advanced API Chaining Example (GoRest API)

### Prerequisites Setup

#### 1. Get Authentication Token
1. Visit `https://gorest.co.in`
2. Click "Get Access Token"
3. Sign in with GitHub/Google/Microsoft
4. Copy the generated token

#### 2. Collection-Level Authorization
```javascript
// Collection → Authorization → Bearer Token
// Token: your_generated_token_here
```

### Complete Chaining Workflow

#### Step 1: Create User (POST)
**URL:** `https://gorest.co.in/public/v2/users`

**Pre-request Script:**
```javascript
// Generate random user data
const random = Math.random().toString(36).substring(2, 8);
const userName = `User_${random}`;
const userEmail = `user_${random}@gmail.com`;

// Set environment variables
pm.environment.set("userName_env", userName);
pm.environment.set("userEmail_env", userEmail);

console.log("Generated user data:", {
    name: userName,
    email: userEmail
});
```

**Request Body:**
```json
{
  "name": "{{userName_env}}",
  "gender": "male",
  "email": "{{userEmail_env}}",
  "status": "inactive"
}
```

**Test Script:**
```javascript
// Extract user ID from response
const jsonData = pm.response.json();
pm.environment.set("userId_env", jsonData.id);

// Validation
pm.test("User created successfully", () => {
    pm.response.to.have.status(201);
    pm.expect(jsonData.id).to.be.a('number');
    pm.expect(jsonData.name).to.equal(pm.environment.get("userName_env"));
    pm.expect(jsonData.email).to.equal(pm.environment.get("userEmail_env"));
});

console.log("Created user ID:", jsonData.id);
```

#### Step 2: Get User Details (GET)
**URL:** `https://gorest.co.in/public/v2/users/{{userId_env}}`

**Test Script:**
```javascript
pm.test("User data validation", () => {
    const jsonData = pm.response.json();
    
    // Validate response structure
    pm.expect(jsonData).to.be.an('object');
    pm.expect(jsonData.id).to.be.a('number');
    pm.expect(jsonData.name).to.be.a('string');
    pm.expect(jsonData.email).to.be.a('string');
    
    // Compare with original data
    pm.expect(jsonData.id).to.equal(parseInt(pm.environment.get("userId_env")));
    pm.expect(jsonData.name).to.equal(pm.environment.get("userName_env"));
    pm.expect(jsonData.email).to.equal(pm.environment.get("userEmail_env"));
});

console.log("Retrieved user data matches original data");
```

#### Step 3: Update User (PUT)
**URL:** `https://gorest.co.in/public/v2/users/{{userId_env}}`

**Pre-request Script:**
```javascript
// Generate new random data for update
const random = Math.random().toString(36).substring(2, 8);
const updatedName = `UpdatedUser_${random}`;
const updatedEmail = `updated_${random}@gmail.com`;

// Update environment variables
pm.environment.set("userName_env", updatedName);
pm.environment.set("userEmail_env", updatedEmail);

console.log("Generated updated user data:", {
    name: updatedName,
    email: updatedEmail
});
```

**Request Body:**
```json
{
  "name": "{{userName_env}}",
  "email": "{{userEmail_env}}",
  "status": "active"
}
```

**Test Script:**
```javascript
pm.test("User updated successfully", () => {
    const jsonData = pm.response.json();
    
    pm.response.to.have.status(200);
    pm.expect(jsonData.name).to.equal(pm.environment.get("userName_env"));
    pm.expect(jsonData.email).to.equal(pm.environment.get("userEmail_env"));
    pm.expect(jsonData.status).to.equal("active");
});

console.log("User updated successfully");
```

#### Step 4: Delete User (DELETE)
**URL:** `https://gorest.co.in/public/v2/users/{{userId_env}}`

**Test Script:**
```javascript
pm.test("User deleted successfully", () => {
    pm.response.to.have.status(204);
});

// Clean up all environment variables
pm.environment.unset("userId_env");
pm.environment.unset("userName_env");
pm.environment.unset("userEmail_env");

console.log("User deleted and variables cleaned up");
```

## Dynamic Data Generation Techniques

### Random String Generation
```javascript
// Generate random alphanumeric string
const randomString = Math.random().toString(36).substring(2, 8);

// Generate random number
const randomNumber = Math.floor(Math.random() * 1000);

// Generate timestamp-based unique value
const timestamp = Date.now();
const uniqueId = `user_${timestamp}`;
```

### Email Generation
```javascript
// Random email with timestamp
const timestamp = Date.now();
const email = `test_${timestamp}@example.com`;

// Random email with string
const random = Math.random().toString(36).substring(2, 8);
const email = `user_${random}@gmail.com`;
```

### Name Generation
```javascript
// Random name with prefix
const random = Math.random().toString(36).substring(2, 8);
const name = `TestUser_${random}`;

// Name with timestamp
const timestamp = Date.now();
const name = `User_${timestamp}`;
```

## Variable Management in Chaining

### Setting Variables
```javascript
// In pre-request scripts
pm.environment.set("variableName", "value");
pm.collectionVariables.set("variableName", "value");
pm.globals.set("variableName", "value");
```

### Getting Variables
```javascript
// In test scripts or pre-request scripts
const value = pm.environment.get("variableName");
const value = pm.collectionVariables.get("variableName");
const value = pm.globals.get("variableName");
```

### Using Variables in Requests
```javascript
// In URL
https://api.example.com/users/{{userId}}

// In request body
{
  "name": "{{userName}}",
  "email": "{{userEmail}}"
}

// In headers
Authorization: Bearer {{authToken}}
```

### Cleaning Up Variables
```javascript
// Remove specific variables
pm.environment.unset("variableName");

// Remove multiple variables
const variablesToClean = ["userId", "userName", "userEmail"];
variablesToClean.forEach(variable => {
    pm.environment.unset(variable);
});
```

## Error Handling in Chaining

### Conditional Variable Setting
```javascript
pm.test("Extract user ID if successful", () => {
    if (pm.response.code === 201) {
        const jsonData = pm.response.json();
        pm.environment.set("userId", jsonData.id);
    } else {
        console.error("User creation failed, skipping ID extraction");
    }
});
```

### Variable Existence Check
```javascript
// Pre-request script
const userId = pm.environment.get("userId");
if (!userId) {
    throw new Error("User ID not found. Please run create user request first.");
}
```

### Graceful Failure Handling
```javascript
pm.test("Handle missing data gracefully", () => {
    try {
        const jsonData = pm.response.json();
        if (jsonData && jsonData.id) {
            pm.environment.set("userId", jsonData.id);
        } else {
            console.warn("Response does not contain expected ID field");
        }
    } catch (error) {
        console.error("Failed to parse response JSON:", error);
    }
});
```

## Collection Runner for Chaining

### Running Chained Collections
1. **Select Collection** → Click three dots → **Run collection**
2. **Set Iteration Count** → Usually 1 for chaining
3. **Add Delay** → 1-2 seconds between requests
4. **Select All Requests** → Ensure proper order
5. **Run Collection** → Monitor execution

### Execution Order
```
1. Create User (POST) → Extract ID
2. Get User (GET) → Validate data
3. Update User (PUT) → Modify data
4. Delete User (DELETE) → Clean up
```

## Best Practices

### 1. Variable Naming
```javascript
// Use descriptive, consistent names
pm.environment.set("userId_env", userId);
pm.environment.set("userName_env", userName);
pm.environment.set("userEmail_env", userEmail);
```

### 2. Data Validation
```javascript
// Always validate extracted data
pm.test("Validate extracted ID", () => {
    const userId = pm.environment.get("userId");
    pm.expect(userId).to.not.be.undefined;
    pm.expect(userId).to.be.a('number');
});
```

### 3. Cleanup Strategy
```javascript
// Clean up in the last request of the chain
pm.environment.unset("userId");
pm.environment.unset("userName");
pm.environment.unset("userEmail");
```

### 4. Error Prevention
```javascript
// Check prerequisites before proceeding
const requiredVars = ["userId", "authToken"];
requiredVars.forEach(varName => {
    const value = pm.environment.get(varName);
    if (!value) {
        throw new Error(`Required variable '${varName}' is missing`);
    }
});
```

### 5. Logging for Debugging
```javascript
// Add console logs for debugging
console.log("Request data:", {
    userId: pm.environment.get("userId"),
    userName: pm.environment.get("userName")
});
```

## Common Chaining Patterns

### 1. CRUD Operations Chain
```
Create → Read → Update → Delete
```

### 2. Authentication Chain
```
Login → Get Token → Access Protected Resource → Logout
```

### 3. E-commerce Chain
```
Create Product → Add to Cart → Calculate Total → Process Payment
```

### 4. User Management Chain
```
Register User → Verify Email → Login → Update Profile → Deactivate
```

## Troubleshooting Chaining Issues

### Issue 1: Variable Not Found
**Problem**: `Variable 'userId' not found`
**Solution**: 
```javascript
// Check if variable exists before using
const userId = pm.environment.get("userId");
if (!userId) {
    console.error("userId not set. Check previous request.");
    return;
}
```

### Issue 2: Wrong Data Type
**Problem**: ID expected as number but stored as string
**Solution**:
```javascript
// Convert data types when needed
const userId = parseInt(pm.environment.get("userId"));
pm.environment.set("userId", userId);
```

### Issue 3: Request Order
**Problem**: Requests executing in wrong order
**Solution**: Use Collection Runner with proper request ordering

### Issue 4: Authentication Failures
**Problem**: Token expired or invalid
**Solution**:
```javascript
// Check response status and handle auth errors
if (pm.response.code === 401) {
    console.error("Authentication failed. Check token.");
    pm.environment.unset("authToken");
}
```

## Advanced Chaining Techniques

### Conditional Chaining
```javascript
// Execute different chains based on conditions
pm.test("Conditional execution", () => {
    const userType = pm.environment.get("userType");
    
    if (userType === "admin") {
        pm.environment.set("nextRequest", "adminOperations");
    } else {
        pm.environment.set("nextRequest", "userOperations");
    }
});
```

### Parallel Data Extraction
```javascript
// Extract multiple values in one test
pm.test("Extract multiple values", () => {
    const jsonData = pm.response.json();
    
    pm.environment.set("userId", jsonData.id);
    pm.environment.set("userName", jsonData.name);
    pm.environment.set("userEmail", jsonData.email);
    pm.environment.set("userStatus", jsonData.status);
});
```

### Data Transformation
```javascript
// Transform data before using in next request
pm.test("Transform data", () => {
    const jsonData = pm.response.json();
    
    // Transform email to uppercase
    const email = jsonData.email.toUpperCase();
    pm.environment.set("userEmail", email);
    
    // Extract domain from email
    const domain = jsonData.email.split('@')[1];
    pm.environment.set("emailDomain", domain);
});
```

## Next Steps

In the next module, we will cover:
1. **Data-Driven Testing**: Using external data files
2. **Collection Runners**: Advanced execution strategies
3. **Newman CLI**: Command-line automation
4. **CI/CD Integration**: Automated testing pipelines
5. **Performance Testing**: Load testing with chained requests

## Practice Exercises

### Exercise 1: Basic Student Chain
1. Create student with dynamic data
2. Retrieve student details
3. Update student information
4. Delete student
5. Validate data consistency throughout

### Exercise 2: GoRest User Management
1. Set up GoRest API authentication
2. Implement complete user CRUD chain
3. Add comprehensive validations
4. Include error handling

### Exercise 3: E-commerce Simulation
1. Create product
2. Add product to cart
3. Update cart quantities
4. Calculate total price
5. Process mock payment

Remember: API chaining is essential for realistic testing scenarios. Practice these patterns to master complex API workflows and dependencies.