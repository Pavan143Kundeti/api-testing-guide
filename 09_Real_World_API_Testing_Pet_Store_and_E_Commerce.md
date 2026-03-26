# 09 Real-World API Testing: Pet Store and E-Commerce APIs

## Pet Store API - Popular Testing API

Pet Store is one of the most popular and famous APIs used for learning and practicing API testing. It provides different types of features and functionalities, supporting both JSON and XML response formats.

### Accessing Pet Store Documentation

**Pet Store Swagger URL:** `https://petstore.swagger.io`

This API provides comprehensive Swagger documentation with interactive testing capabilities.

### Pet Store API Models

The Pet Store API is organized into different models (functional areas):

| Model | Description | Response Format |
|-------|-------------|-----------------|
| **Pet** | Pet management operations | JSON/XML |
| **Store** | Store/order operations | JSON |
| **User** | User management operations | JSON |

### Pet Store API Features

- **Free API** with extensive functionality
- **Interactive Swagger documentation**
- **Multiple response formats** (JSON/XML)
- **Real-world scenarios** for practice
- **No authentication required** for basic operations

## Working with Pet Store User Model (JSON Response)

### User Model APIs

The User model provides the following operations:
- Create user
- Get user by username
- Update user
- Delete user
- User login/logout
- Create list of users

### Example: Create User API

#### Swagger Exploration Process

1. **Access Swagger Documentation**
   ```
   https://petstore.swagger.io
   ```

2. **Navigate to User Model**
   - Click on "User" section
   - Select "POST /user" (Create user)

3. **Explore API Details**
   - Click "Try it out"
   - Review request body structure
   - Modify sample data
   - Click "Execute"

4. **Extract cURL Command**
   ```bash
   curl -X POST "https://petstore.swagger.io/v2/user" \
     -H "accept: application/xml" \
     -H "Content-Type: application/json" \
     -d '{
       "id": 1010,
       "username": "john",
       "firstName": "John",
       "lastName": "Doe",
       "email": "john@example.com",
       "password": "password123",
       "phone": "1234567890",
       "userStatus": 0
     }'
   ```

### Importing Pet Store APIs to Postman

#### Method 1: Import cURL
1. Copy cURL command from Swagger
2. In Postman: **Import** → **Raw Text**
3. Paste cURL command
4. Click **Continue** → **Import**

#### Method 2: Manual Creation
1. Create new collection: "Pet Store"
2. Add request manually:
   - Method: POST
   - URL: `https://petstore.swagger.io/v2/user`
   - Body: JSON format from Swagger

### Dynamic Data Generation

#### Pre-request Script for User Creation
```javascript
// Generate random ID
const randomId = Math.floor(Math.random() * 10000);

// Generate random string
const randomString = Math.random().toString(36).substring(2, 8);

// Set environment variables
pm.environment.set("userId", randomId);
pm.collectionVariables.set("username", "user_" + randomString);
pm.collectionVariables.set("firstName", "First_" + randomString);
pm.collectionVariables.set("lastName", "Last_" + randomString);
pm.collectionVariables.set("email", randomString + "@example.com");
pm.collectionVariables.set("password", "pass_" + randomString);
pm.collectionVariables.set("phone", "123456" + Math.floor(Math.random() * 10000));
pm.collectionVariables.set("userStatus", 0);

// Log generated data
console.log("Generated User ID:", randomId);
console.log("Generated Username:", "user_" + randomString);
```

#### Request Body with Variables
```json
{
  "id": {{userId}},
  "username": "{{username}}",
  "firstName": "{{firstName}}",
  "lastName": "{{lastName}}",
  "email": "{{email}}",
  "password": "{{password}}",
  "phone": "{{phone}}",
  "userStatus": {{userStatus}}
}
```

#### Test Script for User Creation
```javascript
pm.test("User created successfully", () => {
    pm.response.to.have.status(200);
});

pm.test("Response contains user data", () => {
    const jsonData = pm.response.json();
    
    // Validate response structure
    pm.expect(jsonData).to.have.property('id');
    pm.expect(jsonData).to.have.property('username');
    
    // Validate data matches request
    const userId = pm.environment.get("userId");
    pm.expect(jsonData.id).to.equal(userId);
});
```

### API Chaining Example

#### 1. Create User (POST)
```javascript
// Pre-request: Generate user data (as shown above)
// Test: Validate creation and store username for next requests
```

#### 2. Get User by Username (GET)
```javascript
// URL: https://petstore.swagger.io/v2/user/{{username}}
// Test script:
pm.test("User retrieved successfully", () => {
    pm.response.to.have.status(200);
    
    const jsonData = pm.response.json();
    const expectedUsername = pm.collectionVariables.get("username");
    
    pm.expect(jsonData.username).to.equal(expectedUsername);
});
```

#### 3. Update User (PUT)
```javascript
// Pre-request: Generate new email and phone
const newEmail = Math.random().toString(36).substring(2, 8) + "@updated.com";
const newPhone = "999" + Math.floor(Math.random() * 1000000);

pm.collectionVariables.set("updatedEmail", newEmail);
pm.collectionVariables.set("updatedPhone", newPhone);

// Request body: Same as create user but with updated fields
// URL: https://petstore.swagger.io/v2/user/{{username}}
```

#### 4. Delete User (DELETE)
```javascript
// URL: https://petstore.swagger.io/v2/user/{{username}}
// Test script:
pm.test("User deleted successfully", () => {
    pm.response.to.have.status(200);
});

// Clean up variables after deletion
pm.environment.unset("userId");
pm.collectionVariables.unset("username");
pm.collectionVariables.unset("firstName");
pm.collectionVariables.unset("lastName");
pm.collectionVariables.unset("email");
pm.collectionVariables.unset("password");
pm.collectionVariables.unset("phone");
pm.collectionVariables.unset("userStatus");
```

## Working with Pet Store Pet Model (XML Response)

### Pet Model APIs

The Pet model supports XML responses and includes:
- Add new pet to store
- Find pet by ID
- Update existing pet
- Delete pet

### XML Response Handling

#### Converting XML to JSON for Testing
```javascript
// Test script for XML response validation
pm.test("XML Response Validation", function() {
    // Convert XML response to JSON
    const jsonData = xml2Json(pm.response.text());
    
    // Validate pet name
    pm.expect(jsonData.pet.name).to.equal("Jimmy");
    
    // Store pet ID for other requests
    const petId = jsonData.pet.id;
    pm.collectionVariables.set("petId", petId);
});
```

#### XML Request Body Example
```xml
<?xml version="1.0" encoding="UTF-8"?>
<pet>
    <id>{{petId}}</id>
    <category>
        <id>1</id>
        <name>Dogs</name>
    </category>
    <name>Jimmy</name>
    <photoUrls>
        <photoUrl>string</photoUrl>
    </photoUrls>
    <tags>
        <tag>
            <id>0</id>
            <name>string</name>
        </tag>
    </tags>
    <status>available</status>
</pet>
```

### XML Testing Tools

#### Online XML to JSON Converter
Use online tools to understand XML structure:
1. Copy XML response
2. Search "XML to JSON converter online"
3. Convert XML to JSON
4. Use JSON Path Finder to identify paths

#### JSON Path for XML Data
```javascript
// After converting XML to JSON
const jsonData = xml2Json(pm.response.text());

// Access pet ID
const petId = jsonData.pet.id;

// Access pet name
const petName = jsonData.pet.name;

// Access category name
const categoryName = jsonData.pet.category.name;
```

### Complete Pet Model Test Collection

#### 1. Add New Pet (POST)
```javascript
// Pre-request: Generate pet data
const petName = "Pet_" + Math.random().toString(36).substring(2, 6);
pm.collectionVariables.set("petName", petName);

// Test: Validate creation and store pet ID
pm.test("Pet created successfully", () => {
    pm.response.to.have.status(200);
    
    const jsonData = xml2Json(pm.response.text());
    const petId = jsonData.pet.id;
    
    pm.collectionVariables.set("petId", petId);
    pm.expect(jsonData.pet.name).to.equal(petName);
});
```

#### 2. Find Pet by ID (GET)
```javascript
// URL: https://petstore.swagger.io/v2/pet/{{petId}}
// Test: Validate retrieved pet matches stored ID
pm.test("Pet retrieved successfully", () => {
    pm.response.to.have.status(200);
    
    const jsonData = xml2Json(pm.response.text());
    const expectedPetId = pm.collectionVariables.get("petId");
    
    pm.expect(jsonData.pet.id).to.equal(expectedPetId);
});
```

#### 3. Update Pet (PUT)
```javascript
// Request body: XML with updated pet name
// Test: Validate name update
pm.test("Pet updated successfully", () => {
    pm.response.to.have.status(200);
    
    const jsonData = xml2Json(pm.response.text());
    pm.expect(jsonData.pet.name).to.equal("Tommy"); // Updated name
});
```

#### 4. Delete Pet (DELETE)
```javascript
// URL: https://petstore.swagger.io/v2/pet/{{petId}}
// Test: Validate deletion and cleanup
pm.test("Pet deleted successfully", () => {
    pm.response.to.have.status(200);
});

// Clean up variables
pm.collectionVariables.unset("petId");
pm.collectionVariables.unset("petName");
```

## Creating and Publishing Documentation

### Generating Collection Documentation

1. **Access Documentation**
   - Select collection
   - Click three dots → "View documentation"

2. **Review Generated Documentation**
   - Request details (method, URL, parameters)
   - Example requests and responses
   - Descriptions and code samples

3. **Publish Documentation**
   - Click "Publish" in documentation view
   - Choose format and styling options
   - Generate public URL

### Documentation Best Practices

#### Adding Descriptions
```javascript
// Add descriptions to requests and collections
// Example: "This endpoint creates a new user account with the provided details"
```

#### Including Examples
- Save successful responses as examples
- Document different scenarios
- Show error cases and handling

### Sharing Collections

#### Method 1: Published Documentation
1. Publish collection documentation
2. Share public URL
3. Users can click "Run in Postman" to import

#### Method 2: Export Collection
1. Right-click collection → Export
2. Choose collection format version
3. Share exported JSON file

## E-Commerce API Testing with OpenCart

### OpenCart Application Overview

OpenCart is a popular open-source e-commerce platform that provides:
- **Frontend operations**: User registration, product browsing, cart management, ordering
- **Backend operations**: Product management, order processing, customer management, reporting

### OpenCart API Setup Requirements

#### Prerequisites
1. **Local Installation Required**
   - Cannot access APIs from online demo
   - Need admin access to create API users
   - Requires proper server environment

2. **Server Components**
   - **Apache**: Web server for running application
   - **MySQL**: Database for storing application data
   - **PHP**: Server-side scripting language

#### XAMPP Installation
XAMPP provides all required components in one package:

**Download XAMPP:**
- Recommended version: 7.4.29 (for compatibility)
- Avoid latest versions due to compatibility issues
- Available for Windows, Mac, and Linux

**XAMPP Components:**
- Apache (web server)
- MySQL (database)
- PHP (scripting language)
- phpMyAdmin (database management)

### OpenCart Installation Process

#### Step 1: Download OpenCart
```
URL: https://www.opencart.com/index.php?route=cms/download
Recommended Version: 3.0.3.8
```

#### Step 2: Extract and Setup
1. Extract downloaded ZIP file
2. Remove version number from folder name
3. Copy "opencart" folder to XAMPP's htdocs directory
4. Rename config files:
   - `config-dist.php` → `config.php` (in upload folder)
   - `config-dist.php` → `config.php` (in upload/admin folder)

#### Step 3: Database Configuration
1. Start XAMPP (Apache and MySQL)
2. Access phpMyAdmin: `http://localhost/phpmyadmin`
3. Create new database: "openshop"
4. Configure database connection during installation

#### Step 4: OpenCart Installation
1. Access: `http://localhost/opencart/upload`
2. Follow installation wizard
3. Provide database details:
   - Username: root
   - Password: (leave empty)
   - Database: openshop
4. Create admin account
5. Delete install folder after completion

### Creating API User in OpenCart

#### Access Admin Panel
```
URL: http://localhost/opencart/upload/admin
Login: Use credentials created during installation
```

#### Create API User
1. Navigate to **System** → **Users** → **API**
2. Click **Add New** (+) button
3. Configure API user:
   - **Username**: demo_api
   - **API Key**: Click "Generate" button
   - **Status**: Enabled
4. Add IP Address:
   - Click **IP Addresses** tab
   - Add your local IP address
   - Get IP using: `ipconfig` (Windows) or `ifconfig` (Mac/Linux)
5. Save configuration

### OpenCart API Documentation

#### Static Documentation
OpenCart provides static API documentation (no Swagger):
```
Search: "OpenCart API documentation"
```

#### Available API Operations

**Authentication APIs:**
- Login API
- Session management

**Cart APIs:**
- Add product to cart
- Edit cart items
- Update cart quantities
- Remove items from cart
- Get cart contents

**Product APIs:**
- Get product information
- Search products
- Get product options

**Order APIs:**
- Create orders
- Get order details
- Update order status

### Sample OpenCart API Requests

#### Login API
```javascript
// POST request to establish session
// URL: http://localhost/opencart/upload/index.php?route=api/login
// Body:
{
  "username": "demo_api",
  "key": "generated_api_key_here"
}

// Test script:
pm.test("Login successful", () => {
    pm.response.to.have.status(200);
    
    const jsonData = pm.response.json();
    pm.expect(jsonData).to.have.property('api_token');
    
    // Store token for subsequent requests
    pm.collectionVariables.set("api_token", jsonData.api_token);
});
```

#### Add Product to Cart
```javascript
// POST request to add product
// URL: http://localhost/opencart/upload/index.php?route=api/cart/add&api_token={{api_token}}
// Body:
{
  "product_id": "43",
  "quantity": "2"
}

// Test script:
pm.test("Product added to cart", () => {
    pm.response.to.have.status(200);
    
    const jsonData = pm.response.json();
    pm.expect(jsonData.success).to.include("Success");
});
```

## Best Practices for Real-World API Testing

### 1. Documentation Analysis
- **Swagger Documentation**: Interactive, try APIs directly
- **Static Documentation**: Extract endpoints, parameters, examples
- **cURL Import**: Quick way to get started with requests

### 2. Data Management
- **Dynamic Data Generation**: Use pre-request scripts
- **Variable Management**: Environment and collection variables
- **Data Cleanup**: Unset variables after use

### 3. Response Validation
- **JSON Responses**: Direct validation with pm.expect()
- **XML Responses**: Convert to JSON first, then validate
- **Status Codes**: Always validate HTTP status codes
- **Data Integrity**: Verify request data matches response data

### 4. API Chaining
- **Sequential Operations**: Create → Read → Update → Delete
- **Data Flow**: Pass data between requests using variables
- **Error Handling**: Validate each step before proceeding

### 5. Collection Organization
- **Logical Grouping**: Organize by functionality/model
- **Naming Conventions**: Clear, descriptive request names
- **Documentation**: Add descriptions and examples
- **Publishing**: Share collections with team

### 6. Testing Strategy
- **Individual Testing**: Test each request separately first
- **Collection Testing**: Run entire collection after individual validation
- **Environment Testing**: Test across different environments
- **Regression Testing**: Regular execution to catch issues

## Next Steps

In the next module, we will cover:
1. **Advanced Collection Running**: Newman CLI, Jenkins integration
2. **HTML Report Generation**: Professional reporting
3. **Performance Testing**: Response time validation
4. **Mock Servers**: Creating mock APIs for testing
5. **CI/CD Integration**: Automated API testing in pipelines

## Practice Exercises

### Exercise 1: Pet Store Complete Testing
1. Create complete Pet Store collection (User and Pet models)
2. Implement dynamic data generation
3. Add comprehensive validations
4. Test both JSON and XML responses
5. Publish documentation

### Exercise 2: OpenCart E-commerce Testing
1. Install OpenCart locally
2. Create API user and configure access
3. Implement cart management flow
4. Test product operations
5. Create comprehensive test collection

### Exercise 3: Documentation and Sharing
1. Create detailed documentation for both collections
2. Publish collections with proper descriptions
3. Export collections for sharing
4. Import published collections from URLs

Remember: Real-world API testing requires understanding business workflows, proper data management, and comprehensive validation strategies. Practice with these popular APIs to build strong testing skills.