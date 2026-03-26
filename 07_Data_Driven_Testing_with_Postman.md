# 07 Data-Driven Testing with Postman

## What is Data-Driven Testing?

Data-Driven Testing is a testing approach where test data is stored in external files (CSV or JSON) and used to execute the same test multiple times with different data sets. Instead of hardcoding values in requests, we use variables that get populated from external data sources at runtime.

### Key Benefits
- **Reusability**: Same test logic with multiple data sets
- **Maintainability**: Easy to update test data without changing scripts
- **Coverage**: Test various scenarios with different inputs
- **Efficiency**: Execute multiple test cases in one run
- **Separation of Concerns**: Test logic separate from test data

### Data Sources Supported
- **CSV Files**: Comma-separated values format
- **JSON Files**: JavaScript Object Notation format

## Books API Example

We'll use a Books API to demonstrate data-driven testing concepts. This API provides endpoints for managing books and orders.

### API Documentation Overview

**Base URL:** `https://simple-books-api.glitch.me`

**Available Endpoints:**
- `GET /status` - API status (no auth required)
- `GET /books` - List all books (no auth required)
- `GET /books/:id` - Get single book (no auth required)
- `POST /api-clients` - Create API token (no auth required)
- `POST /orders` - Submit order (auth required)
- `GET /orders` - Get all orders (auth required)
- `GET /orders/:id` - Get single order (auth required)
- `PATCH /orders/:id` - Update order (auth required)
- `DELETE /orders/:id` - Delete order (auth required)

### Authentication Setup

#### Step 1: Create API Token
**Request:**
```
POST https://simple-books-api.glitch.me/api-clients
Content-Type: application/json

{
  "clientName": "API Testing Training",
  "clientEmail": "training@gmail.com"
}
```

**Response:**
```json
{
  "accessToken": "your-generated-token-here"
}
```

#### Step 2: Use Token for Protected Endpoints
Add Bearer token to Authorization header for order-related requests.

## Setting Up Data-Driven Tests

### Step 1: Prepare Test Data

#### CSV Format (books-test-data.csv)
```csv
bookId,customerName
1,John Doe
3,Jane Smith
4,Mike Johnson
6,Sarah Wilson
1,Tom Brown
```

#### JSON Format (books-test-data.json)
```json
[
  {
    "bookId": 1,
    "customerName": "John Doe"
  },
  {
    "bookId": 3,
    "customerName": "Jane Smith"
  },
  {
    "bookId": 4,
    "customerName": "Mike Johnson"
  },
  {
    "bookId": 6,
    "customerName": "Sarah Wilson"
  },
  {
    "bookId": 1,
    "customerName": "Tom Brown"
  }
]
```

**Important Notes:**
- Column names in CSV become variable names in Postman
- Book IDs must exist and be available in the API
- Customer names can be any valid string

### Step 2: Create Collection Structure

Create a new collection called "Books API Data Driven" with these requests:
1. **Submit Order** (POST) - Creates order with data from file
2. **Get Single Order** (GET) - Retrieves created order details
3. **Delete Order** (DELETE) - Removes the created order

## Implementing Data-Driven Requests

### Request 1: Submit Order (POST)

**URL:** `https://simple-books-api.glitch.me/orders`

**Authorization:** Bearer Token (from API token creation)

**Request Body:**
```json
{
  "bookId": {{bookId}},
  "customerName": "{{customerName}}"
}
```

**Pre-request Script:**
```javascript
// Optional: Log the data being used
console.log("Using data:", {
    bookId: pm.iterationData.get("bookId"),
    customerName: pm.iterationData.get("customerName")
});
```

**Test Script:**
```javascript
// Validate status code
pm.test("Order created successfully", () => {
    pm.response.to.have.status(201);
});

// Extract order ID for chaining
pm.test("Extract order ID", () => {
    const jsonData = pm.response.json();
    
    // Validate response structure
    pm.expect(jsonData).to.have.property('created', true);
    pm.expect(jsonData).to.have.property('orderId');
    
    // Store order ID for next requests
    pm.environment.set("orderId_env", jsonData.orderId);
    
    console.log("Created order ID:", jsonData.orderId);
});

// Validate request data matches response
pm.test("Validate order data", () => {
    const jsonData = pm.response.json();
    const requestBookId = parseInt(pm.iterationData.get("bookId"));
    
    // Note: Response might not include all request data
    // Add validations based on actual API response structure
    pm.expect(jsonData.orderId).to.be.a('string');
});
```

### Request 2: Get Single Order (GET)

**URL:** `https://simple-books-api.glitch.me/orders/{{orderId_env}}`

**Authorization:** Bearer Token

**Test Script:**
```javascript
// Validate status code
pm.test("Order retrieved successfully", () => {
    pm.response.to.have.status(200);
});

// Validate order data
pm.test("Validate order details", () => {
    const jsonData = pm.response.json();
    
    // Validate response structure
    pm.expect(jsonData).to.be.an('object');
    pm.expect(jsonData).to.have.property('id');
    pm.expect(jsonData).to.have.property('bookId');
    pm.expect(jsonData).to.have.property('customerName');
    
    // Compare with environment variable
    const expectedOrderId = pm.environment.get("orderId_env");
    pm.expect(jsonData.id).to.equal(expectedOrderId);
    
    // Compare with iteration data
    const expectedBookId = parseInt(pm.iterationData.get("bookId"));
    const expectedCustomerName = pm.iterationData.get("customerName");
    
    pm.expect(jsonData.bookId).to.equal(expectedBookId);
    pm.expect(jsonData.customerName).to.equal(expectedCustomerName);
    
    console.log("Order details validated successfully");
});
```

### Request 3: Delete Order (DELETE)

**URL:** `https://simple-books-api.glitch.me/orders/{{orderId_env}}`

**Authorization:** Bearer Token

**Test Script:**
```javascript
// Validate status code
pm.test("Order deleted successfully", () => {
    pm.response.to.have.status(204);
});

// Clean up environment variables
pm.test("Cleanup environment variables", () => {
    pm.environment.unset("orderId_env");
    console.log("Environment variables cleaned up");
});
```

## Running Data-Driven Tests

### Using Collection Runner

#### Step 1: Access Collection Runner
1. Click **three dots** on collection → **Run collection**
2. Collection Runner interface opens

#### Step 2: Configure Run Settings

**Iterations:**
- Set number of iterations based on data rows
- Example: 5 iterations for 5 data rows

**Delay:**
- Add delay between requests (2-3 seconds recommended)
- Prevents server overload and ensures proper execution

**Data File:**
- Click **Select File**
- Browse and select your CSV or JSON file
- Click **Preview** to verify data structure

#### Step 3: Verify Data Preview
```
Iteration 1: bookId=1, customerName="John Doe"
Iteration 2: bookId=3, customerName="Jane Smith"
Iteration 3: bookId=4, customerName="Mike Johnson"
Iteration 4: bookId=6, customerName="Sarah Wilson"
Iteration 5: bookId=1, customerName="Tom Brown"
```

#### Step 4: Execute Collection
1. Click **Run [Collection Name]**
2. Monitor execution progress
3. View results summary

### Results Analysis

#### Execution Summary
- **Total Iterations:** Number of data rows processed
- **Total Requests:** Iterations × Requests per iteration
- **Passed Tests:** Number of successful assertions
- **Failed Tests:** Number of failed assertions
- **Execution Time:** Total time taken

#### Detailed Results
- **Per Iteration:** View results for each data set
- **Per Request:** See individual request/response details
- **Console Logs:** Debug information and custom logs
- **Test Results:** Pass/fail status for each assertion

## Advanced Data-Driven Techniques

### Accessing Iteration Data in Scripts

#### Get Current Iteration Data
```javascript
// Get data for current iteration
const bookId = pm.iterationData.get("bookId");
const customerName = pm.iterationData.get("customerName");

console.log("Current iteration data:", {
    bookId: bookId,
    customerName: customerName
});
```

#### Conditional Logic Based on Data
```javascript
// Different logic based on data values
const bookId = pm.iterationData.get("bookId");

if (bookId === 1) {
    // Special handling for book ID 1
    pm.environment.set("specialBook", "true");
} else {
    pm.environment.set("specialBook", "false");
}
```

### Dynamic Request Modification

#### Modify Headers Based on Data
```javascript
// Pre-request script
const customerName = pm.iterationData.get("customerName");

if (customerName.includes("VIP")) {
    pm.request.headers.add({
        key: "X-Priority",
        value: "high"
    });
}
```

#### Conditional Request Body
```javascript
// Pre-request script
const bookId = pm.iterationData.get("bookId");
const customerName = pm.iterationData.get("customerName");

// Add special instructions for certain books
let requestBody = {
    bookId: parseInt(bookId),
    customerName: customerName
};

if (bookId === "1") {
    requestBody.specialInstructions = "Handle with care";
}

pm.request.body.raw = JSON.stringify(requestBody);
```

### Data Validation and Error Handling

#### Validate Data Before Use
```javascript
// Pre-request script
const bookId = pm.iterationData.get("bookId");
const customerName = pm.iterationData.get("customerName");

// Validate required data
if (!bookId || !customerName) {
    throw new Error("Missing required data: bookId or customerName");
}

// Validate data types
if (isNaN(parseInt(bookId))) {
    throw new Error("Invalid bookId: must be a number");
}

if (customerName.length < 2) {
    throw new Error("Invalid customerName: must be at least 2 characters");
}
```

#### Handle Missing Data Gracefully
```javascript
// Test script
pm.test("Handle data gracefully", () => {
    const bookId = pm.iterationData.get("bookId");
    
    if (bookId) {
        // Normal validation
        const jsonData = pm.response.json();
        pm.expect(jsonData.bookId).to.equal(parseInt(bookId));
    } else {
        // Skip validation if data missing
        console.warn("BookId not provided, skipping validation");
    }
});
```

## Complex Data Structures

### Nested JSON Data
```json
[
  {
    "order": {
      "bookId": 1,
      "customerName": "John Doe",
      "preferences": {
        "delivery": "express",
        "gift": true
      }
    },
    "metadata": {
      "testCase": "TC001",
      "priority": "high"
    }
  }
]
```

**Accessing Nested Data:**
```javascript
// Access nested properties
const bookId = pm.iterationData.get("order.bookId");
const customerName = pm.iterationData.get("order.customerName");
const delivery = pm.iterationData.get("order.preferences.delivery");
const testCase = pm.iterationData.get("metadata.testCase");
```

### Array Data in CSV
```csv
bookId,customerName,tags
1,"John Doe","fiction,bestseller,new"
3,"Jane Smith","non-fiction,educational"
```

**Processing Array Data:**
```javascript
// Pre-request script
const tagsString = pm.iterationData.get("tags");
const tagsArray = tagsString ? tagsString.split(",") : [];

// Use in request body
const requestBody = {
    bookId: parseInt(pm.iterationData.get("bookId")),
    customerName: pm.iterationData.get("customerName"),
    tags: tagsArray
};
```

## Best Practices

### Data File Management
1. **Use Descriptive Names**: `books-order-test-data.csv`
2. **Version Control**: Keep data files in version control
3. **Validate Data**: Check data integrity before testing
4. **Document Structure**: Comment data file format and purpose

### Test Design
1. **Atomic Tests**: Each iteration should be independent
2. **Cleanup**: Remove test data after each iteration
3. **Error Handling**: Handle invalid data gracefully
4. **Logging**: Add meaningful console logs for debugging

### Performance Considerations
1. **Reasonable Delays**: Don't overload the server
2. **Batch Size**: Limit iterations for large datasets
3. **Resource Cleanup**: Clean up created resources
4. **Monitor Execution**: Watch for timeouts or failures

### Data Validation
```javascript
// Comprehensive data validation
pm.test("Validate iteration data", () => {
    const bookId = pm.iterationData.get("bookId");
    const customerName = pm.iterationData.get("customerName");
    
    // Check data exists
    pm.expect(bookId, "BookId is required").to.not.be.undefined;
    pm.expect(customerName, "CustomerName is required").to.not.be.undefined;
    
    // Check data types
    pm.expect(parseInt(bookId), "BookId must be a number").to.be.a('number');
    pm.expect(customerName, "CustomerName must be a string").to.be.a('string');
    
    // Check data ranges
    pm.expect(parseInt(bookId), "BookId must be positive").to.be.above(0);
    pm.expect(customerName.length, "CustomerName must not be empty").to.be.above(0);
});
```

## Troubleshooting Common Issues

### Issue 1: Data Not Loading
**Problem**: Variables not populated from data file
**Solutions**:
- Check file format (CSV headers, JSON structure)
- Verify file selection in Collection Runner
- Use Preview to validate data structure

### Issue 2: Type Conversion Errors
**Problem**: String vs Number type mismatches
**Solutions**:
```javascript
// Convert string to number
const bookId = parseInt(pm.iterationData.get("bookId"));

// Handle potential NaN
const bookId = parseInt(pm.iterationData.get("bookId")) || 0;
```

### Issue 3: Missing Data Handling
**Problem**: Some iterations have missing data
**Solutions**:
```javascript
// Check for data existence
const bookId = pm.iterationData.get("bookId");
if (bookId) {
    // Process normally
} else {
    console.warn("BookId missing for this iteration");
    return; // Skip this iteration
}
```

### Issue 4: Authentication Failures
**Problem**: Token expires during long runs
**Solutions**:
- Refresh token in pre-request script
- Use collection-level authentication
- Monitor token expiration

## Reporting and Analysis

### Export Results
1. **JSON Format**: Detailed programmatic analysis
2. **HTML Report**: Visual summary report
3. **CSV Export**: Spreadsheet analysis

### Custom Reporting
```javascript
// Test script - Custom metrics
pm.test("Custom metrics", () => {
    const responseTime = pm.response.responseTime;
    const iteration = pm.info.iteration;
    
    // Log performance data
    console.log(`Iteration ${iteration}: Response time ${responseTime}ms`);
    
    // Set custom metrics
    pm.globals.set(`iteration_${iteration}_time`, responseTime);
});
```

## Integration with CI/CD

### Newman Command Line
```bash
# Run collection with data file
newman run collection.json -d data.csv --reporters cli,json

# With environment file
newman run collection.json -d data.csv -e environment.json

# With custom options
newman run collection.json -d data.csv --iteration-count 5 --delay-request 2000
```

## Next Steps

In the next module, we will cover:
1. **Collection Runners Advanced Features**
2. **Newman CLI for Automation**
3. **Performance Testing with Data**
4. **CI/CD Pipeline Integration**
5. **Advanced Reporting and Analytics**

## Practice Exercises

### Exercise 1: Basic Data-Driven Test
1. Create CSV file with book orders data
2. Set up collection with submit/get/delete order flow
3. Run with 3 different data sets
4. Validate all assertions pass

### Exercise 2: Complex Data Structures
1. Create JSON file with nested order data
2. Include customer preferences and metadata
3. Access nested properties in scripts
4. Validate complex response structures

### Exercise 3: Error Handling
1. Include invalid data in test file
2. Add data validation in pre-request scripts
3. Handle errors gracefully
4. Generate meaningful error reports

Remember: Data-driven testing is powerful for comprehensive API validation. Start simple and gradually add complexity as you master the concepts.