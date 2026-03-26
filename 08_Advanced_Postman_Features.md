# 08 Advanced Postman Features

## File Upload Testing

File upload testing is essential when APIs handle file operations. Many web applications use APIs internally to handle file uploads, making it crucial to test these endpoints directly.

### Setting Up File Upload API

For demonstration, we'll use a Spring Boot application packaged as a JAR file that provides file upload endpoints.

#### Prerequisites
- Java installed on your system
- File upload REST API JAR file

#### Starting the API Server
```bash
# Navigate to the directory containing the JAR file
java -jar file-upload-rest-api.jar
```

**Expected Output:**
```
Started FileUploadApplication on port 8080
```

#### API Endpoints
- **Single File Upload:** `POST http://localhost:8080/uploadFile`
- **Multiple Files Upload:** `POST http://localhost:8080/uploadMultipleFiles`
- **UI Interface:** `http://localhost:8080` (for manual testing)

### Testing Single File Upload

#### Request Configuration
**Method:** POST  
**URL:** `http://localhost:8080/uploadFile`

**Body Configuration:**
1. Go to **Body** tab
2. Select **form-data**
3. Set key as `file` (this is the parameter name expected by the API)
4. Change dropdown from "Text" to "File"
5. Click **Select Files** and choose your file

**Example Request:**
```
POST http://localhost:8080/uploadFile
Content-Type: multipart/form-data

file: test1.txt (selected file)
```

**Expected Response:**
```json
{
  "fileName": "test1.txt",
  "fileDownloadUri": "http://localhost:8080/downloadFile/test1.txt",
  "fileType": "text/plain",
  "size": 1024
}
```

#### Test Script for File Upload
```javascript
pm.test("File uploaded successfully", () => {
    pm.response.to.have.status(200);
});

pm.test("Response contains file information", () => {
    const jsonData = pm.response.json();
    
    pm.expect(jsonData).to.have.property('fileName');
    pm.expect(jsonData).to.have.property('fileDownloadUri');
    pm.expect(jsonData).to.have.property('fileType');
    pm.expect(jsonData).to.have.property('size');
    
    // Validate file name matches uploaded file
    pm.expect(jsonData.fileName).to.equal('test1.txt');
    pm.expect(jsonData.size).to.be.above(0);
});

pm.test("Download URI is valid", () => {
    const jsonData = pm.response.json();
    const downloadUri = jsonData.fileDownloadUri;
    
    pm.expect(downloadUri).to.include('http://localhost:8080/downloadFile/');
    pm.expect(downloadUri).to.include(jsonData.fileName);
});
```

### Testing Multiple File Upload

#### Request Configuration
**Method:** POST  
**URL:** `http://localhost:8080/uploadMultipleFiles`

**Body Configuration:**
1. Go to **Body** tab
2. Select **form-data**
3. Set key as `files` (note the plural form)
4. Change dropdown to "File"
5. Select multiple files

**Example Request:**
```
POST http://localhost:8080/uploadMultipleFiles
Content-Type: multipart/form-data

files: test1.txt, test2.txt (multiple files selected)
```

**Expected Response:**
```json
[
  {
    "fileName": "test1.txt",
    "fileDownloadUri": "http://localhost:8080/downloadFile/test1.txt",
    "fileType": "text/plain",
    "size": 1024
  },
  {
    "fileName": "test2.txt",
    "fileDownloadUri": "http://localhost:8080/downloadFile/test2.txt",
    "fileType": "text/plain",
    "size": 2048
  }
]
```

#### Test Script for Multiple File Upload
```javascript
pm.test("Multiple files uploaded successfully", () => {
    pm.response.to.have.status(200);
});

pm.test("Response is an array", () => {
    const jsonData = pm.response.json();
    pm.expect(jsonData).to.be.an('array');
    pm.expect(jsonData.length).to.be.above(0);
});

pm.test("Each file has required properties", () => {
    const jsonData = pm.response.json();
    
    jsonData.forEach((file, index) => {
        pm.expect(file, `File ${index + 1}`).to.have.property('fileName');
        pm.expect(file, `File ${index + 1}`).to.have.property('fileDownloadUri');
        pm.expect(file, `File ${index + 1}`).to.have.property('fileType');
        pm.expect(file, `File ${index + 1}`).to.have.property('size');
        pm.expect(file.size, `File ${index + 1} size`).to.be.above(0);
    });
});
```

### Form Data vs Other Body Types

#### Form Data
- Used for file uploads and form submissions
- Supports both text and file inputs
- Automatically sets `Content-Type: multipart/form-data`
- Key-value pairs sent as form fields

#### When to Use Form Data
- File upload operations
- Simulating HTML form submissions
- APIs expecting `multipart/form-data` content type

#### File vs Text Selection
- **File**: Browse and select files from your system
- **Text**: Manually enter file path as text (less common)

## Authentication Types in Postman

Authentication is crucial for accessing protected APIs. Postman supports multiple authentication methods to handle various security requirements.

### Authentication Types Overview

| Type | Use Case | Requirements | Security Level |
|------|----------|--------------|----------------|
| **No Auth** | Public APIs | None | None |
| **API Key** | Simple token-based | API key | Medium |
| **Bearer Token** | Token-based auth | Access token | Medium |
| **Basic Auth** | Username/password | Credentials | Low |
| **Digest Auth** | Enhanced basic auth | Credentials | Medium |
| **OAuth 1.0** | Third-party auth | Client credentials | High |
| **OAuth 2.0** | Modern third-party auth | Client credentials | High |

### 1. Basic Authentication

Simple username and password authentication where credentials are sent with each request.

#### Configuration
1. Go to **Authorization** tab
2. Select **Basic Auth**
3. Enter username and password

#### Example
```javascript
// Postman handles encoding automatically
Username: postman
Password: password
```

#### Test Script
```javascript
pm.test("Basic authentication successful", () => {
    pm.response.to.have.status(200);
    
    const jsonData = pm.response.json();
    pm.expect(jsonData.authenticated).to.be.true;
});
```

#### Security Note
Basic authentication sends credentials in Base64 encoding (not encryption). Always use HTTPS in production.

### 2. Digest Authentication

Enhanced version of basic authentication where credentials are hashed before transmission.

#### Configuration
1. Go to **Authorization** tab
2. Select **Digest Auth**
3. Enter username and password
4. Leave advanced fields to Postman (auto-filled)

#### Key Differences from Basic Auth
- Credentials are hashed using MD5
- Includes nonce and other security parameters
- More secure than basic authentication
- Postman handles the complexity automatically

#### Test Script
```javascript
pm.test("Digest authentication successful", () => {
    pm.response.to.have.status(200);
    
    const jsonData = pm.response.json();
    pm.expect(jsonData.authenticated).to.be.true;
});

// Check if digest headers were sent
pm.test("Digest headers present", () => {
    const request = pm.request;
    // Postman automatically adds digest headers
    console.log("Authentication headers added by Postman");
});
```

### 3. API Key Authentication

Token-based authentication where a key is passed as a parameter or header.

#### Configuration
1. Go to **Authorization** tab
2. Select **API Key**
3. Enter key name and value
4. Choose location (Header/Query Parameter)

#### Example: Weather API
```javascript
// API Key configuration
Key: appid
Value: your-api-key-here
Add to: Query Params
```

**Request URL:**
```
GET https://api.openweathermap.org/data/2.5/weather?q=Delhi&appid=your-api-key
```

#### Test Script
```javascript
pm.test("API key authentication successful", () => {
    pm.response.to.have.status(200);
});

pm.test("Weather data received", () => {
    const jsonData = pm.response.json();
    
    pm.expect(jsonData).to.have.property('name');
    pm.expect(jsonData).to.have.property('weather');
    pm.expect(jsonData).to.have.property('main');
    
    console.log(`Weather in ${jsonData.name}: ${jsonData.weather[0].description}`);
});
```

### 4. Bearer Token Authentication

Token-based authentication commonly used with JWT tokens and OAuth.

#### Configuration
1. Go to **Authorization** tab
2. Select **Bearer Token**
3. Enter the token value

#### Example: GitHub API
```javascript
// Bearer Token configuration
Token: ghp_your-github-token-here
```

**Request:**
```
GET https://api.github.com/user/repos
Authorization: Bearer ghp_your-github-token-here
```

#### Creating GitHub Token
1. Go to GitHub → Settings → Developer settings
2. Personal access tokens → Generate new token
3. Select required scopes (repo, user, etc.)
4. Copy the generated token

#### Test Script
```javascript
pm.test("Bearer token authentication successful", () => {
    pm.response.to.have.status(200);
});

pm.test("Repository data received", () => {
    const jsonData = pm.response.json();
    
    pm.expect(jsonData).to.be.an('array');
    
    if (jsonData.length > 0) {
        const repo = jsonData[0];
        pm.expect(repo).to.have.property('name');
        pm.expect(repo).to.have.property('full_name');
        pm.expect(repo).to.have.property('private');
        
        console.log(`Found ${jsonData.length} repositories`);
    }
});
```

### 5. OAuth 2.0 Authentication

Modern, secure authentication standard for third-party access.

#### OAuth 2.0 Flow Overview
```
1. Client requests authorization from resource owner
2. Resource owner grants authorization
3. Client receives authorization grant
4. Client requests access token from authorization server
5. Authorization server authenticates client and validates grant
6. Authorization server issues access token
7. Client uses access token to access protected resources
```

#### Setting Up OAuth 2.0 (GitHub Example)

**Step 1: Create OAuth App**
1. GitHub → Settings → Developer settings → OAuth Apps
2. Click "New OAuth App"
3. Fill in application details:
   - Application name: "API Testing App"
   - Homepage URL: `http://localhost:3000`
   - Authorization callback URL: `http://localhost:3000/callback`
4. Register application
5. Note down Client ID and Client Secret

**Step 2: Authorization Request**
```
GET https://github.com/login/oauth/authorize?client_id=YOUR_CLIENT_ID&scope=repo
```

**Step 3: Exchange Code for Token**
```
POST https://github.com/login/oauth/access_token
Content-Type: application/json

{
  "client_id": "YOUR_CLIENT_ID",
  "client_secret": "YOUR_CLIENT_SECRET",
  "code": "AUTHORIZATION_CODE_FROM_STEP_2"
}
```

**Step 4: Use Access Token**
```
GET https://api.github.com/user
Authorization: token YOUR_ACCESS_TOKEN
```

#### OAuth 2.0 in Postman
1. Go to **Authorization** tab
2. Select **OAuth 2.0**
3. Configure grant type and parameters
4. Click "Get New Access Token"
5. Complete authorization flow
6. Use token for API requests

### Collection-Level Authentication

Set authentication at collection level to apply to all requests:

1. Select collection
2. Go to **Authorization** tab
3. Configure authentication
4. Individual requests inherit from parent (default)

#### Override Collection Authentication
For specific requests that need different auth:
1. Go to request **Authorization** tab
2. Select different auth type
3. Configure request-specific authentication

## API Documentation

### Swagger/OpenAPI Documentation

Swagger is an interactive API documentation standard that allows developers to document and test APIs.

#### What is Swagger?
- **Interactive Documentation**: Test APIs directly from documentation
- **Standardized Format**: OpenAPI specification
- **Auto-generated**: Often generated from code annotations
- **Comprehensive**: Includes endpoints, parameters, responses, examples

#### Swagger vs Static Documentation

| Feature | Swagger | Static Documentation |
|---------|---------|---------------------|
| **Interactivity** | Can test APIs directly | Read-only |
| **Format** | Web-based interface | PDF/Word documents |
| **Updates** | Auto-sync with code | Manual updates required |
| **Testing** | Built-in testing capability | No testing features |
| **Examples** | Live examples with real data | Static examples only |

#### Using Swagger Documentation

**Example Swagger Interface:**
```
GET /api/activities
Parameters: None
Responses:
  200: Success
    Schema: Array of Activity objects
  400: Bad Request
  500: Internal Server Error
```

**Testing in Swagger:**
1. Click "Try it out"
2. Enter required parameters
3. Click "Execute"
4. View response data, headers, and status

#### Importing from Swagger to Postman

**Method 1: Copy cURL**
1. Execute request in Swagger
2. Copy the generated cURL command
3. In Postman: Import → Raw Text → Paste cURL
4. Click Continue → Import

**Method 2: OpenAPI Import**
1. Get Swagger JSON/YAML URL
2. In Postman: Import → Link
3. Enter Swagger specification URL
4. Import entire API collection

### cURL Commands

cURL (Client URL) is a command-line tool for making HTTP requests.

#### What is cURL?
- **Command-line tool** for transferring data
- **Supports multiple protocols** (HTTP, HTTPS, FTP, etc.)
- **Cross-platform** available on all operating systems
- **Scriptable** for automation

#### cURL Syntax
```bash
curl [options] [URL]
```

#### Common cURL Examples

**GET Request:**
```bash
curl https://api.example.com/users
```

**POST Request with JSON:**
```bash
curl -X POST \
  https://api.example.com/users \
  -H "Content-Type: application/json" \
  -d '{"name":"John","email":"john@example.com"}'
```

**With Authentication:**
```bash
curl -H "Authorization: Bearer token123" \
  https://api.example.com/protected
```

**File Upload:**
```bash
curl -X POST \
  -F "file=@/path/to/file.txt" \
  https://api.example.com/upload
```

#### Converting cURL to Postman

**From cURL to Postman:**
1. Copy cURL command
2. Postman → Import → Raw Text
3. Paste cURL command
4. Click Continue → Import
5. Request is automatically created with all parameters

**From Postman to cURL:**
1. Send request in Postman
2. Click "Code" link below Send button
3. Select cURL from dropdown
4. Copy generated cURL command

### Creating Documentation in Postman

After completing API testing, create documentation for your collections.

#### Generating Collection Documentation

**Step 1: Access Documentation**
1. Select collection
2. Click three dots → "View documentation"
3. Documentation opens in new tab

**Step 2: Documentation Features**
- **Request details**: Method, URL, parameters
- **Example requests/responses**: Actual data from tests
- **Descriptions**: Add context and explanations
- **Code samples**: Multiple language examples

**Step 3: Publishing Documentation**
1. Click "Publish" in documentation view
2. Choose visibility settings
3. Generate public URL
4. Share with team or stakeholders

#### Documentation Best Practices

**Add Descriptions:**
```javascript
// In request description
This endpoint creates a new user account.
Required fields: name, email
Optional fields: phone, address
```

**Include Examples:**
- Save successful responses as examples
- Document error scenarios
- Show different parameter combinations

**Organize Collections:**
- Use folders for logical grouping
- Name requests descriptively
- Order requests logically (CRUD operations)

#### Documentation Formats

**HTML Documentation:**
- Web-based interface
- Interactive examples
- Searchable content
- Responsive design

**Markdown Export:**
- Static documentation
- Version control friendly
- Easy to integrate with wikis

**JSON Export:**
- Machine-readable format
- Integration with other tools
- Backup and versioning

## Advanced Testing Scenarios

### File Upload with Validation

```javascript
// Pre-request script for file upload
pm.test("File upload validation", () => {
    // Validate file size (example: max 5MB)
    const maxSize = 5 * 1024 * 1024; // 5MB in bytes
    
    // This would be set based on actual file
    const fileSize = pm.environment.get("uploadFileSize");
    
    if (fileSize && fileSize > maxSize) {
        throw new Error("File size exceeds maximum limit");
    }
});

// Test script for file upload response
pm.test("File upload successful", () => {
    pm.response.to.have.status(200);
    
    const response = pm.response.json();
    
    // Validate response structure
    pm.expect(response).to.have.property('fileName');
    pm.expect(response).to.have.property('fileType');
    pm.expect(response).to.have.property('size');
    
    // Validate file type
    const allowedTypes = ['text/plain', 'image/jpeg', 'image/png'];
    pm.expect(allowedTypes).to.include(response.fileType);
    
    // Store file info for cleanup
    pm.environment.set("uploadedFileName", response.fileName);
});
```

### Authentication Flow Testing

```javascript
// OAuth 2.0 flow testing
pm.test("OAuth 2.0 Authorization Flow", () => {
    // Step 1: Authorization request
    const authUrl = "https://provider.com/oauth/authorize";
    const clientId = pm.environment.get("oauth_client_id");
    const redirectUri = pm.environment.get("oauth_redirect_uri");
    
    const authRequest = `${authUrl}?client_id=${clientId}&redirect_uri=${redirectUri}&response_type=code`;
    
    console.log("Authorization URL:", authRequest);
    
    // Step 2: Exchange code for token (in separate request)
    // Step 3: Use token for API access (in subsequent requests)
});

// Token validation
pm.test("Token is valid", () => {
    const token = pm.environment.get("access_token");
    
    pm.expect(token).to.not.be.undefined;
    pm.expect(token).to.have.length.above(10);
    
    // Check token expiration if available
    const expiresAt = pm.environment.get("token_expires_at");
    if (expiresAt) {
        const now = Date.now();
        pm.expect(parseInt(expiresAt)).to.be.above(now);
    }
});
```

### Multi-step Authentication

```javascript
// Collection pre-request script for token refresh
const tokenExpiry = pm.environment.get("token_expires_at");
const currentTime = Date.now();

if (!tokenExpiry || currentTime >= parseInt(tokenExpiry)) {
    console.log("Token expired or missing, refreshing...");
    
    // This would trigger token refresh logic
    pm.environment.set("needs_token_refresh", "true");
} else {
    pm.environment.set("needs_token_refresh", "false");
}
```

## Best Practices

### File Upload Testing
1. **Test various file types**: Text, images, documents
2. **Validate file size limits**: Test boundary conditions
3. **Check error handling**: Invalid files, oversized files
4. **Verify file integrity**: Ensure uploaded files are not corrupted
5. **Test concurrent uploads**: Multiple files simultaneously

### Authentication Testing
1. **Test all auth methods**: Verify each authentication type works
2. **Handle token expiration**: Implement refresh logic
3. **Test invalid credentials**: Verify proper error responses
4. **Secure token storage**: Use environment variables for sensitive data
5. **Test authorization levels**: Different user permissions

### Documentation
1. **Keep documentation updated**: Sync with API changes
2. **Include examples**: Real request/response data
3. **Document error cases**: Common failure scenarios
4. **Use clear descriptions**: Explain purpose and usage
5. **Version documentation**: Track changes over time

## Next Steps

In the next module, we will cover:
1. **Collection Sharing and Collaboration**
2. **Newman CLI for Automation**
3. **Monitoring and Performance Testing**
4. **Integration with CI/CD Pipelines**
5. **Advanced Reporting and Analytics**

## Practice Exercises

### Exercise 1: File Upload API
1. Set up the file upload API server
2. Test single and multiple file uploads
3. Add comprehensive validations
4. Test error scenarios (invalid files, size limits)

### Exercise 2: Authentication Methods
1. Set up APIs with different authentication types
2. Test basic, digest, API key, and bearer token auth
3. Implement OAuth 2.0 flow
4. Add proper error handling for auth failures

### Exercise 3: Documentation Creation
1. Create comprehensive collection documentation
2. Add descriptions and examples to all requests
3. Publish documentation and share with team
4. Export documentation in multiple formats

Remember: Advanced features like file uploads and authentication are crucial for real-world API testing. Master these concepts to handle complex testing scenarios effectively.