# 04 API Response Validation and Testing

## Response Validation Overview

When testing APIs, we need to validate multiple components of the response to ensure the API is working correctly. Before adding validation points, you must:

1. **Understand the expected response** structure and data
2. **Run the API at least once** to analyze the actual response
3. **Compare actual vs expected** results
4. **Add appropriate validation points** based on requirements

## Components to Validate

### 1. Status Code
- **2xx series**: Success responses (200, 201, 204)
- **4xx series**: Client errors (400, 401, 404)
- **5xx series**: Server errors (500, 502, 503)

### 2. Response Headers
- **Content-Type**: Data format (application/json)
- **Content-Length**: Response size
- **Custom headers**: API-specific headers

### 3. Cookies
- **Cookie presence**: Verify expected cookies exist
- **Cookie values**: Validate cookie data

### 4. Response Time
- **Performance validation**: Response within acceptable limits
- **Timeout handling**: Ensure reasonable response times

### 5. Response Body
- **Data accuracy**: Correct values returned
- **Data types**: Proper JSON structure
- **Schema validation**: Response follows expected format

## Postman Assertions Framework

### PM Library
Postman provides a built-in library called **PM** (Postman) with pre-defined functions for validation.

### Chai Assertion Library
Postman uses the **Chai** assertion framework (JavaScript testing framework) for writing test assertions.

### Function Syntax Options

**Normal Function:**
```javascript
pm.test("Test Description", function () {
    // Assertion code here
});
```

**Arrow Function (Recommended):**
```javascript
pm.test("Test Description", () => {
    // Assertion code here
});
```

## Status Code Validation

### Basic Status Code Test
```javascript
pm.test("Status code is 200", () => {
    pm.response.to.have.status(200);
});
```

### Multiple Status Code Options
```javascript
pm.test("Status code is success", () => {
    pm.expect(pm.response.code).to.be.oneOf([200, 201, 202]);
});
```

### Status Code with Message
```javascript
pm.test("Status code is 201 Created", () => {
    pm.response.to.have.status("Created");
});
```

### Practical Example
```javascript
// Test for GET request
pm.test("GET request returns 200", () => {
    pm.response.to.have.status(200);
});

// Test for POST request
pm.test("POST request returns 201", () => {
    pm.response.to.have.status(201);
});

// Test for DELETE request
pm.test("DELETE request returns 204", () => {
    pm.response.to.have.status(204);
});
```

## Header Validation

### Check Header Presence
```javascript
pm.test("Content-Type header is present", () => {
    pm.response.to.have.header("Content-Type");
});
```

### Validate Header Value
```javascript
pm.test("Content-Type is application/json", () => {
    pm.expect(pm.response.headers.get("Content-Type")).to.equal("application/json; charset=utf-8");
});
```

### Multiple Header Validations
```javascript
pm.test("Response headers validation", () => {
    pm.response.to.have.header("Content-Type");
    pm.response.to.have.header("Content-Length");
    pm.expect(pm.response.headers.get("Content-Type")).to.include("application/json");
});
```

## Cookie Validation

### Check Cookie Presence
```javascript
pm.test("Language cookie is present", () => {
    pm.expect(pm.cookies.has("language")).to.be.true;
});
```

### Validate Cookie Value
```javascript
pm.test("Language cookie value is correct", () => {
    pm.expect(pm.cookies.get("language")).to.equal("en-GB");
});
```

### Multiple Cookie Tests
```javascript
pm.test("Cookie validation", () => {
    pm.expect(pm.cookies.has("currency")).to.be.true;
    pm.expect(pm.cookies.get("currency")).to.equal("USD");
    pm.expect(pm.cookies.has("language")).to.be.true;
    pm.expect(pm.cookies.get("language")).to.equal("en-GB");
});
```

## Response Time Validation

### Response Time Below Threshold
```javascript
pm.test("Response time is less than 200ms", () => {
    pm.expect(pm.response.responseTime).to.be.below(200);
});
```

### Response Time Above Threshold
```javascript
pm.test("Response time is acceptable", () => {
    pm.expect(pm.response.responseTime).to.be.above(10);
    pm.expect(pm.response.responseTime).to.be.below(1000);
});
```

## Response Body Validation

### Capture Response Data
First, capture the entire JSON response into a variable:

```javascript
// Global variable (outside test functions)
const jsonData = pm.response.json();

// Or inside test function
pm.test("Response body validation", () => {
    const jsonData = pm.response.json();
    // Validation code here
});
```

### 1. Data Type Validation

**Validate JSON Structure:**
```javascript
pm.test("Response data types are correct", () => {
    const jsonData = pm.response.json();
    
    // Validate entire response is an object
    pm.expect(jsonData).to.be.an('object');
    
    // Validate individual field types
    pm.expect(jsonData.id).to.be.a('number');
    pm.expect(jsonData.name).to.be.a('string');
    pm.expect(jsonData.location).to.be.a('string');
    pm.expect(jsonData.phone).to.be.a('string');
    pm.expect(jsonData.courses).to.be.an('array');
});
```

### 2. Array Property Validation

**Single Value in Array:**
```javascript
pm.test("Array contains expected value", () => {
    const jsonData = pm.response.json();
    pm.expect(jsonData.courses).to.include("JavaScript");
});
```

**Multiple Values in Array:**
```javascript
pm.test("Array contains all expected values", () => {
    const jsonData = pm.response.json();
    pm.expect(jsonData.courses).to.have.members(["JavaScript", "Python"]);
});
```

### 3. Field Value Validation

**Exact Value Matching:**
```javascript
pm.test("Field values are correct", () => {
    const jsonData = pm.response.json();
    
    pm.expect(jsonData.id).to.equal(1);
    pm.expect(jsonData.name).to.equal("John Doe");
    pm.expect(jsonData.location).to.equal("New York");
    pm.expect(jsonData.phone).to.equal("1234567890");
    pm.expect(jsonData.courses[0]).to.equal("JavaScript");
    pm.expect(jsonData.courses[1]).to.equal("Python");
});
```

**Complex JSON Path:**
For nested JSON structures, use proper JSON path:
```javascript
pm.test("Nested data validation", () => {
    const jsonData = pm.response.json();
    
    // For array of objects: students[0].name
    pm.expect(jsonData.students[0].name).to.equal("John");
    pm.expect(jsonData.students[1].courses[0]).to.equal("Java");
});
```

### 4. JSON Schema Validation

**Generate JSON Schema:**
Use online tools like `jsonschema.net` to generate schema from your JSON response.

**Schema Validation Code:**
```javascript
pm.test("JSON schema is valid", () => {
    const jsonData = pm.response.json();
    
    // Define expected schema
    const schema = {
        "type": "object",
        "properties": {
            "id": {
                "type": "integer"
            },
            "name": {
                "type": "string"
            },
            "location": {
                "type": "string"
            },
            "phone": {
                "type": "string"
            },
            "courses": {
                "type": "array",
                "items": {
                    "type": "string"
                }
            }
        },
        "required": ["id", "name", "location", "phone", "courses"]
    };
    
    // Validate response against schema
    pm.expect(tv4.validate(jsonData, schema)).to.be.true;
});
```

## Complete Validation Example

### GET Request Validation
```javascript
// Capture response data globally
const jsonData = pm.response.json();

// Status code validation
pm.test("Status code is 200", () => {
    pm.response.to.have.status(200);
});

// Header validation
pm.test("Headers are correct", () => {
    pm.response.to.have.header("Content-Type");
    pm.expect(pm.response.headers.get("Content-Type")).to.include("application/json");
});

// Response time validation
pm.test("Response time is acceptable", () => {
    pm.expect(pm.response.responseTime).to.be.below(1000);
});

// Data type validation
pm.test("Response data types are correct", () => {
    pm.expect(jsonData).to.be.an('object');
    pm.expect(jsonData.id).to.be.a('number');
    pm.expect(jsonData.name).to.be.a('string');
    pm.expect(jsonData.courses).to.be.an('array');
});

// Field value validation
pm.test("Field values are correct", () => {
    pm.expect(jsonData.id).to.equal(1);
    pm.expect(jsonData.name).to.equal("John Doe");
    pm.expect(jsonData.location).to.equal("New York");
});

// Array validation
pm.test("Courses array is correct", () => {
    pm.expect(jsonData.courses).to.include("JavaScript");
    pm.expect(jsonData.courses).to.have.members(["JavaScript", "Python"]);
});
```

### POST Request Validation
```javascript
pm.test("POST request validation", () => {
    // Status code for creation
    pm.response.to.have.status(201);
    
    const jsonData = pm.response.json();
    
    // Verify created data
    pm.expect(jsonData.name).to.equal("New Student");
    pm.expect(jsonData.id).to.be.a('number');
    pm.expect(jsonData.id).to.be.above(0);
});
```

### PUT Request Validation
```javascript
pm.test("PUT request validation", () => {
    pm.response.to.have.status(200);
    
    const jsonData = pm.response.json();
    
    // Verify updated data
    pm.expect(jsonData.name).to.equal("Updated Name");
    pm.expect(jsonData.location).to.equal("Updated Location");
});
```

### DELETE Request Validation
```javascript
pm.test("DELETE request validation", () => {
    // DELETE typically returns 204 No Content
    pm.response.to.have.status(204);
    
    // Response time should be reasonable
    pm.expect(pm.response.responseTime).to.be.below(500);
});
```

## Validation Best Practices

### 1. Test Organization
- **Group related tests** in single test functions when appropriate
- **Use descriptive test names** that clearly indicate what's being tested
- **Separate concerns**: Different test functions for different validation types

### 2. Error Handling
- **One failed assertion fails the entire test function**
- **Use multiple test functions** for independent validations
- **Provide clear error messages** in test descriptions

### 3. Dynamic Data Handling
- **Use variables** for dynamic values (IDs, timestamps)
- **Focus on static validations** for predictable data
- **Validate data patterns** rather than exact values when appropriate

### 4. Reusability
- **Create reusable test templates** for common validations
- **Use environment variables** for configurable values
- **Share validation patterns** across team members

## Running Collection Tests

### Individual Request Testing
1. Add validation code in the **Tests** tab
2. Click **Send** to execute request and tests
3. View results in **Test Results** tab

### Collection Runner
1. Click **three dots** on collection → **Run collection**
2. Select requests to include
3. Set iterations and delays
4. Click **Run [Collection Name]**
5. View comprehensive test results

### Test Results Analysis
- **Green checkmarks**: Passed tests
- **Red X marks**: Failed tests
- **Detailed error messages**: Show expected vs actual values
- **Response times**: Performance metrics for each request

## Common Validation Patterns

### API Health Check
```javascript
pm.test("API is healthy", () => {
    pm.response.to.have.status(200);
    pm.expect(pm.response.responseTime).to.be.below(2000);
    pm.response.to.have.header("Content-Type");
});
```

### Data Integrity Check
```javascript
pm.test("Data integrity is maintained", () => {
    const jsonData = pm.response.json();
    
    // Required fields are present
    pm.expect(jsonData).to.have.property('id');
    pm.expect(jsonData).to.have.property('name');
    
    // Data types are correct
    pm.expect(jsonData.id).to.be.a('number');
    pm.expect(jsonData.name).to.be.a('string');
    
    // Values are reasonable
    pm.expect(jsonData.id).to.be.above(0);
    pm.expect(jsonData.name).to.have.length.above(0);
});
```

### Security Headers Check
```javascript
pm.test("Security headers are present", () => {
    pm.response.to.have.header("Content-Type");
    pm.expect(pm.response.headers.get("Content-Type")).to.include("application/json");
    
    // Check for security headers if applicable
    if (pm.response.headers.has("X-Frame-Options")) {
        pm.expect(pm.response.headers.get("X-Frame-Options")).to.not.be.empty;
    }
});
```

## Troubleshooting Common Issues

### Issue 1: JSON Parsing Errors
**Problem**: `pm.response.json()` fails
**Solution**: Check if response is valid JSON format

```javascript
pm.test("Response is valid JSON", () => {
    pm.expect(() => pm.response.json()).to.not.throw();
});
```

### Issue 2: Dynamic Values
**Problem**: Values change between requests (timestamps, IDs)
**Solution**: Validate patterns instead of exact values

```javascript
pm.test("Dynamic values are valid", () => {
    const jsonData = pm.response.json();
    
    // Validate ID is a positive number
    pm.expect(jsonData.id).to.be.a('number').and.to.be.above(0);
    
    // Validate timestamp format
    pm.expect(jsonData.createdAt).to.match(/^\d{4}-\d{2}-\d{2}T\d{2}:\d{2}:\d{2}/);
});
```

### Issue 3: Array Order Variations
**Problem**: Array elements in different order
**Solution**: Use `include` or `members` instead of exact index matching

```javascript
pm.test("Array contains expected items", () => {
    const jsonData = pm.response.json();
    
    // Order doesn't matter
    pm.expect(jsonData.courses).to.have.members(["JavaScript", "Python"]);
    
    // Check if specific item exists
    pm.expect(jsonData.courses).to.include("JavaScript");
});
```

## Next Steps

In the next module, we will cover:
1. **Environment Variables and Data Management**
2. **Pre-request Scripts and Dynamic Data**
3. **Collection Variables and Scope**
4. **Advanced Postman Features**
5. **API Testing Automation Strategies**

## Practice Exercises

### Exercise 1: Basic Validation
Create tests for a student API that validate:
- Status code is 200
- Response time under 500ms
- Content-Type is application/json
- Student name is a string
- Student ID is a number

### Exercise 2: Complex JSON Validation
For a nested JSON response, create tests that:
- Validate the overall structure
- Check array contents
- Verify nested object properties
- Validate data types at all levels

### Exercise 3: Error Scenario Testing
Create tests for error scenarios:
- 404 Not Found responses
- 400 Bad Request with validation errors
- 500 Internal Server Error handling
- Timeout scenarios

Remember: Comprehensive validation ensures your APIs work correctly and meet requirements. Practice these validation techniques with different API responses to build expertise.