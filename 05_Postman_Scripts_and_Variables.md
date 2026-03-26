# 05 Postman Scripts and Variables

## Script Types in Postman

Postman provides two main places to write scripts that execute at different times during the request lifecycle:

### 1. Pre-request Scripts
- **Execution Time**: Before sending the request
- **Purpose**: Prepare data, set variables, configure request parameters
- **Location**: Pre-request Script tab in requests, folders, or collections

### 2. Test Scripts
- **Execution Time**: After receiving the response
- **Purpose**: Validate responses, extract data, clean up variables
- **Location**: Tests tab in requests, folders, or collections

## Script Execution Order

Scripts can be defined at three levels with a specific execution hierarchy:

### Execution Hierarchy
```
1. Collection Level Pre-request Script
2. Folder Level Pre-request Script  
3. Request Level Pre-request Script
4. → REQUEST SENT ←
5. → RESPONSE RECEIVED ←
6. Collection Level Test Script
7. Folder Level Test Script
8. Request Level Test Script
```

### Practical Example

**Collection Level Pre-request:**
```javascript
console.log("Pre-request script at collection level");
```

**Folder Level Pre-request:**
```javascript
console.log("Pre-request script at folder level");
```

**Request Level Pre-request:**
```javascript
console.log("Pre-request script at request level");
```

**Collection Level Test:**
```javascript
console.log("Test script at collection level");
```

**Folder Level Test:**
```javascript
console.log("Test script at folder level");
```

**Request Level Test:**
```javascript
console.log("Test script at request level");
```

## Postman Console

The Postman Console displays script execution results and request details:

### Accessing Console
- Click the **Console** button at the bottom of Postman
- View script outputs, request/response details, and execution logs

### Console Logging
```javascript
console.log("Your message here");
console.log("Variable value:", variableValue);
```

## Variables in Postman

Variables eliminate duplication and make collections maintainable by storing reusable values.

### Why Use Variables?
- **Avoid duplication**: Common URLs, tokens, IDs used across requests
- **Easy maintenance**: Change once, update everywhere
- **Environment switching**: Different values for dev/test/prod
- **Dynamic data**: Generate values at runtime

### Variable Types and Scope

| Variable Type | Scope | Access Level | Storage Location |
|---------------|-------|--------------|------------------|
| **Global** | Entire workspace | All collections & requests | Postman workspace |
| **Collection** | Within collection | All requests in collection | Collection settings |
| **Environment** | All collections | When environment is active | Environment settings |
| **Local** | Single request | Current request only | Pre-request script |
| **Data** | Test iteration | During collection runs | External files |

## Variable Syntax

### Setting Variables in UI
Variables can be set through Postman's interface in various locations.

### Referencing Variables
```javascript
// In request URLs, headers, body
{{variableName}}

// Example
{{baseUrl}}/api/users/{{userId}}
```

### Setting Variables in Scripts
```javascript
// Global variables
pm.globals.set("variableName", "value");

// Collection variables  
pm.collectionVariables.set("variableName", "value");

// Environment variables
pm.environment.set("variableName", "value");

// Local variables
pm.variables.set("variableName", "value");
```

### Getting Variables in Scripts
```javascript
// Global variables
const globalVar = pm.globals.get("variableName");

// Collection variables
const collectionVar = pm.collectionVariables.get("variableName");

// Environment variables  
const envVar = pm.environment.get("variableName");

// Local variables
const localVar = pm.variables.get("variableName");
```

### Unsetting Variables
```javascript
// Remove global variable
pm.globals.unset("variableName");

// Remove collection variable
pm.collectionVariables.unset("variableName");

// Remove environment variable
pm.environment.unset("variableName");

// Local variables are automatically removed after request
```

## Global Variables

### Creating Global Variables (UI)
1. Click the **eye icon** (👁️) in top right
2. Click **Edit** next to Globals
3. Add variable name and value
4. Click **Save**

### Creating Global Variables (Script)
```javascript
// In pre-request script
pm.globals.set("baseUrl", "http://localhost:3000");
pm.globals.set("apiKey", "abc123");

// Using in request
// URL: {{baseUrl}}/api/users
// Header: Authorization: Bearer {{apiKey}}
```

### Example: Dynamic Global Variable
```javascript
// Pre-request script
const timestamp = Date.now();
pm.globals.set("requestId", `req_${timestamp}`);

// Use in request body
{
  "requestId": "{{requestId}}",
  "data": "test"
}
```

## Collection Variables

### Creating Collection Variables (UI)
1. Select collection
2. Click **three dots** → **Edit**
3. Go to **Variables** tab
4. Add variable name and value
5. Click **Save**

### Creating Collection Variables (Script)
```javascript
// Pre-request script
pm.collectionVariables.set("collectionId", "users_collection");
pm.collectionVariables.set("version", "v1");

// Use in request
// URL: {{baseUrl}}/{{version}}/users
```

### Example: Collection-Level Configuration
```javascript
// Collection pre-request script
pm.collectionVariables.set("timeout", "5000");
pm.collectionVariables.set("retries", "3");

// Request pre-request script
const timeout = pm.collectionVariables.get("timeout");
console.log(`Request timeout: ${timeout}ms`);
```

## Environment Variables

### Creating Environments (UI)
1. Click **eye icon** (👁️) in top right
2. Click **Add** next to Environments
3. Name your environment (e.g., "Development", "Production")
4. Add variables and values
5. Click **Save**

### Switching Environments
1. Click environment dropdown (top right)
2. Select desired environment
3. Variables from that environment become available

### Creating Environment Variables (Script)
```javascript
// Pre-request script (creates in currently active environment)
pm.environment.set("apiUrl", "https://api.dev.example.com");
pm.environment.set("dbConnection", "dev-database");

// Use in request
// URL: {{apiUrl}}/users
```

### Example: Environment-Specific Configuration
```javascript
// Development environment
pm.environment.set("baseUrl", "http://localhost:3000");
pm.environment.set("debugMode", "true");

// Production environment  
pm.environment.set("baseUrl", "https://api.production.com");
pm.environment.set("debugMode", "false");
```

## Local Variables

Local variables exist only during the current request execution and are created in pre-request scripts.

### Creating Local Variables
```javascript
// Pre-request script
pm.variables.set("requestTimestamp", new Date().toISOString());
pm.variables.set("randomId", Math.floor(Math.random() * 1000));

// Use in request
// Header: X-Request-Time: {{requestTimestamp}}
// Body: {"id": {{randomId}}}
```

### Example: Dynamic Request Data
```javascript
// Pre-request script
const userData = {
    name: `User_${Math.floor(Math.random() * 1000)}`,
    email: `user${Date.now()}@example.com`,
    age: Math.floor(Math.random() * 50) + 18
};

pm.variables.set("userName", userData.name);
pm.variables.set("userEmail", userData.email);
pm.variables.set("userAge", userData.age);

// Request body
{
  "name": "{{userName}}",
  "email": "{{userEmail}}",
  "age": {{userAge}}
}
```

## Practical Examples

### Example 1: API Base URL Management
```javascript
// Global variable for common base
pm.globals.set("protocol", "https");
pm.globals.set("domain", "api.example.com");

// Environment-specific paths
// Development environment
pm.environment.set("basePath", "/dev/v1");

// Production environment  
pm.environment.set("basePath", "/v1");

// Request URL: {{protocol}}://{{domain}}{{basePath}}/users
```

### Example 2: Authentication Token Management
```javascript
// Pre-request script (Collection level)
const token = pm.environment.get("authToken");
if (!token) {
    // Get new token logic here
    pm.environment.set("authToken", "new_token_value");
}

// Request header: Authorization: Bearer {{authToken}}
```

### Example 3: Dynamic Test Data Generation
```javascript
// Pre-request script
const testUser = {
    id: Math.floor(Math.random() * 10000),
    name: `TestUser_${Date.now()}`,
    email: `test_${Date.now()}@example.com`,
    phone: `555-${Math.floor(Math.random() * 9000) + 1000}`
};

// Set as local variables
Object.keys(testUser).forEach(key => {
    pm.variables.set(key, testUser[key]);
});

// Request body
{
  "id": {{id}},
  "name": "{{name}}",
  "email": "{{email}}",
  "phone": "{{phone}}"
}
```

### Example 4: Variable Cleanup After Request
```javascript
// Test script - cleanup after request
pm.test("Cleanup variables", () => {
    // Remove sensitive data
    pm.environment.unset("tempPassword");
    pm.globals.unset("sessionToken");
    
    // Log cleanup
    console.log("Variables cleaned up successfully");
});
```

## Variable Precedence

When multiple variables have the same name, Postman follows this precedence order:

1. **Data variables** (highest precedence)
2. **Local variables**
3. **Environment variables**
4. **Collection variables**
5. **Global variables** (lowest precedence)

### Example: Variable Precedence
```javascript
// If all these exist with same name "apiUrl":
pm.globals.set("apiUrl", "global.api.com");           // Precedence: 5
pm.collectionVariables.set("apiUrl", "collection.api.com"); // Precedence: 4
pm.environment.set("apiUrl", "env.api.com");          // Precedence: 3
pm.variables.set("apiUrl", "local.api.com");          // Precedence: 2

// {{apiUrl}} will resolve to "local.api.com"
```

## Advanced Variable Techniques

### Conditional Variable Setting
```javascript
// Pre-request script
const environment = pm.environment.get("env");

if (environment === "development") {
    pm.variables.set("debugLevel", "verbose");
    pm.variables.set("timeout", "10000");
} else if (environment === "production") {
    pm.variables.set("debugLevel", "error");
    pm.variables.set("timeout", "5000");
}
```

### Variable Validation
```javascript
// Pre-request script
const requiredVars = ["baseUrl", "apiKey", "userId"];

requiredVars.forEach(varName => {
    const value = pm.variables.get(varName);
    if (!value) {
        throw new Error(`Required variable '${varName}' is not set`);
    }
});
```

### Variable Transformation
```javascript
// Pre-request script
const rawUrl = pm.environment.get("rawApiUrl");
const cleanUrl = rawUrl.replace(/\/+$/, ""); // Remove trailing slashes
pm.variables.set("apiUrl", cleanUrl);

const userEmail = pm.variables.get("userEmail");
const domain = userEmail.split('@')[1];
pm.variables.set("emailDomain", domain);
```

## Best Practices

### 1. Variable Naming Conventions
```javascript
// Use descriptive names
pm.globals.set("apiBaseUrl", "https://api.example.com");  // Good
pm.globals.set("url", "https://api.example.com");        // Avoid

// Use consistent naming patterns
pm.environment.set("auth_token", "token123");
pm.environment.set("auth_expires", "2024-12-31");
pm.environment.set("auth_refresh", "refresh456");
```

### 2. Environment Organization
```javascript
// Development environment
pm.environment.set("api_base_url", "http://localhost:3000");
pm.environment.set("database_url", "localhost:5432");
pm.environment.set("debug_enabled", "true");

// Production environment
pm.environment.set("api_base_url", "https://api.production.com");
pm.environment.set("database_url", "prod-db.example.com:5432");
pm.environment.set("debug_enabled", "false");
```

### 3. Sensitive Data Handling
```javascript
// Use environment variables for sensitive data
pm.environment.set("api_secret", "secret_key_here");

// Clear sensitive variables after use
pm.test("Cleanup", () => {
    pm.environment.unset("temp_password");
    pm.variables.unset("session_token");
});
```

### 4. Variable Documentation
```javascript
// Document variable purpose in scripts
// Set user authentication token for API requests
pm.environment.set("auth_token", token);

// Generate unique request ID for tracing
pm.variables.set("request_id", `req_${Date.now()}`);
```

## Debugging Variables

### Logging Variable Values
```javascript
// Pre-request script
console.log("=== Variable Debug Info ===");
console.log("Global baseUrl:", pm.globals.get("baseUrl"));
console.log("Environment:", pm.environment.name);
console.log("Collection var:", pm.collectionVariables.get("version"));
console.log("Local timestamp:", pm.variables.get("timestamp"));
```

### Variable Existence Check
```javascript
// Check if variable exists before using
const apiKey = pm.environment.get("apiKey");
if (!apiKey) {
    console.error("API key not found in environment variables");
    // Handle missing variable
}
```

## Common Use Cases

### 1. API Chaining (Preview)
```javascript
// First request - Get user ID
pm.test("Extract user ID", () => {
    const response = pm.response.json();
    pm.environment.set("userId", response.id);
});

// Second request URL: {{baseUrl}}/users/{{userId}}/profile
```

### 2. Dynamic Headers
```javascript
// Pre-request script
const timestamp = new Date().toISOString();
const signature = generateSignature(timestamp); // Custom function

pm.variables.set("requestTimestamp", timestamp);
pm.variables.set("requestSignature", signature);

// Headers:
// X-Timestamp: {{requestTimestamp}}
// X-Signature: {{requestSignature}}
```

### 3. Test Data Management
```javascript
// Pre-request script for test data
const testData = {
    validEmail: "test@example.com",
    invalidEmail: "invalid-email",
    testPassword: "Test123!",
    testUserId: 12345
};

Object.keys(testData).forEach(key => {
    pm.collectionVariables.set(key, testData[key]);
});
```

## Next Steps

In the next module, we will cover:
1. **API Chaining**: Using response data from one request in subsequent requests
2. **Data-Driven Testing**: Using external data files with variables
3. **Advanced Scripting**: Complex pre-request and test scenarios
4. **Collection Runners**: Automated execution with variables
5. **Newman Integration**: Command-line execution with environments

## Practice Exercises

### Exercise 1: Basic Variable Setup
1. Create a global variable for API base URL
2. Create environment variables for dev and prod
3. Create collection variables for API version
4. Use all variables in a request URL

### Exercise 2: Dynamic Data Generation
1. Create pre-request script that generates random user data
2. Use generated data in POST request body
3. Extract response data and store in variables
4. Clean up variables in test script

### Exercise 3: Environment Switching
1. Create two environments (Development, Production)
2. Set different API URLs and configurations
3. Test same collection in both environments
4. Verify different behaviors based on environment

Remember: Variables are fundamental to efficient API testing. Master these concepts as they're essential for advanced Postman features like API chaining and data-driven testing.