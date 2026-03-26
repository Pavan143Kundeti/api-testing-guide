# 02 Postman Introduction and Setup

## What is Postman?

**Postman** is an API testing tool that allows us to:
- Perform **manual testing** of APIs
- Test both **REST APIs** and **SOAP APIs**
- Create API documentation
- Share collections with team members

### Key Features
- Desktop and Web versions available
- Same UI and functionality across platforms
- Automatic synchronization between versions
- Free tool with comprehensive features

## Postman Versions

### Desktop Version
- Download and install permanently
- Works offline after installation
- Recommended for regular use

### Web Version
- Access through browser URL
- Requires internet connection
- Same workspace synchronization

**Note:** Both versions share the same workspace - changes in one automatically reflect in the other.

## Prerequisites

- **Gmail Account** - Required for Postman account creation
- **Internet Connection** - For initial setup and synchronization

## Installation Guide

### Step 1: Download Postman
1. Go to Google and search "download Postman"
2. Visit official website: `https://www.postman.com/downloads/`
3. Select your operating system:
   - **Windows**: Windows 64-bit
   - **Mac**: Intel chip or Apple chip (M1/M2)

### Step 2: Installation Process
1. Download the installer (executable file for Windows, DMG for Mac)
2. Double-click the installer
3. Choose account option:
   - **Create free account** (for new users)
   - **Sign in** (for existing users)

### Step 3: Account Setup
**Option 1: Sign up with Google**
- Click "Sign up with Google"
- Use your Gmail account

**Option 2: Create new account**
- Provide email, username, and password
- Remember credentials for future logins

### Step 4: First Launch
After successful installation, Postman opens with the main interface.

## Postman Interface Overview

### Main Menu Options
- **Home** - Dashboard and recent activity
- **Workspaces** - Manage different work environments
- **API Network** - Explore public APIs
- **Explore** - Discover templates and examples

### Left Sidebar
- **Collections** - Organize API requests
- **APIs** - API specifications
- **Environments** - Manage variables
- **Mock Servers** - Create mock responses
- **Monitors** - Schedule API tests

## Core Concepts

### 1. Workspace
**Definition:** An area where you maintain all files, test cases, and requests.

**Key Points:**
- Everything in Postman is saved in workspaces
- Workspaces are stored in Google cloud (linked to your Gmail)
- You can create multiple workspaces
- First step when starting with Postman

#### Workspace Visibility Types
| Type | Description | Access |
|------|-------------|---------|
| **Personal** | Only you can access | Private to you |
| **Private** | You + invited team members | Controlled access |
| **Team** | All team members by default | Team collaboration |
| **Public** | Anyone can see and import | Open access |

#### Workspace Operations

**Create Workspace:**
1. Go to **Workspaces** → **Create Workspace**
2. Enter workspace name
3. Add summary (optional)
4. Select visibility type
5. Click **Create Workspace**

**Rename Workspace:**
1. Click on workspace name
2. Edit the name directly
3. Changes save automatically

**Delete Workspace:**
1. Click on workspace name
2. Select **Workspace Settings**
3. Click **Delete Workspace**
4. Confirm by typing workspace name

### 2. Collection
**Definition:** A folder that contains HTTP requests and subfolders.

**Key Points:**
- Collections organize your API requests
- One workspace can have multiple collections
- Collections can contain folders and requests
- Collections can be executed as a group

#### Collection Operations

**Create Collection:**
- **Method 1:** Click **Collections** → **Create Collection**
- **Method 2:** **File** → **New** → **Collection**
- **Method 3:** Click **+** button → **Collection**

**Collection Management:**
- **Rename:** Click three dots → **Rename**
- **Delete:** Click three dots → **Delete**
- **Duplicate:** Click three dots → **Duplicate**
- **Export:** Click three dots → **Export** (saves as JSON file)
- **Import:** **Import** button → select JSON file

**Run Collection:**
- Click three dots → **Run Collection**
- Select requests to include
- Set iterations and delays
- Click **Run [Collection Name]**

## HTTP Request Types

### Request Methods Overview
| Method | Purpose | Example Use Case |
|--------|---------|------------------|
| **GET** | Retrieve data from server | Browse websites, search results |
| **POST** | Create new resource | User registration, create record |
| **PUT** | Update existing resource | Edit profile, update complete record |
| **PATCH** | Update partial resource | Change only salary or designation |
| **DELETE** | Remove resource | Delete user account |

### URL Components

Example URL: `https://reqres.in/api/users?page=2`

| Component | Name | Description |
|-----------|------|-------------|
| `https` | Protocol/Scheme | Communication method |
| `reqres.in` | Host/Domain | Server location |
| `/api/users` | Path Parameters | Resource location |
| `?page=2` | Query Parameters | Filter conditions |

**Path Parameters:** Define folder structure on server
**Query Parameters:** Filter data (after `?` symbol)

## Creating HTTP Requests

### Step 1: Create Request
1. Select collection
2. Click **Add a request** or **+** button
3. Choose request method (GET, POST, PUT, DELETE)
4. Enter request name

### Step 2: Configure Request
1. **Enter URL** in the address bar
2. **Set Parameters** (automatically parsed from URL)
3. **Add Headers** if required
4. **Add Body** for POST/PUT requests

### Step 3: Send Request
1. Click **Send** button
2. View response in bottom panel
3. Analyze status code, response time, and data size

## Request Examples

### GET Request Example
```
Method: GET
URL: https://reqres.in/api/users?page=2
Body: Not required
```

**Response Analysis:**
- **Status Code:** 200 (Success)
- **Response Body:** JSON data with user list
- **Headers:** Content-Type, Date, etc.

### POST Request Example
```
Method: POST
URL: https://reqres.in/api/users
Body: {
    "name": "John Doe",
    "job": "Developer"
}
```

**Configuration:**
1. Select **Body** tab
2. Choose **raw** format
3. Select **JSON** type
4. Enter request payload

**Response Analysis:**
- **Status Code:** 201 (Created)
- **Response Body:** Includes generated ID and timestamp

### PUT Request Example
```
Method: PUT
URL: https://reqres.in/api/users/411
Body: {
    "name": "John Doe",
    "job": "Senior Developer"
}
```

**Key Points:**
- Include record ID in URL path
- Send complete updated data in body
- Status Code: 200 (Updated)

### DELETE Request Example
```
Method: DELETE
URL: https://reqres.in/api/users/411
Body: Not required
```

**Response Analysis:**
- **Status Code:** 204 (No Content)
- **Response Body:** Empty (record deleted)

## Response Validation Areas

### 1. Status Code
**Success Codes (2xx series):**
- **200:** OK - Request successful
- **201:** Created - Resource created successfully
- **202:** Accepted - Request accepted for processing
- **204:** No Content - Successful deletion

**Client Error Codes (4xx series):**
- **400:** Bad Request - Invalid request format
- **401:** Unauthorized - Authentication required
- **403:** Forbidden - Access denied
- **404:** Not Found - Resource doesn't exist

**Server Error Codes (5xx series):**
- **500:** Internal Server Error - Server problem
- **502:** Bad Gateway - Server communication issue
- **503:** Service Unavailable - Server temporarily down

### 2. Response Body
- **JSON Format:** Most common for REST APIs
- **XML Format:** Used by some APIs
- **Content Validation:** Verify expected data structure
- **Data Accuracy:** Check returned values

### 3. Response Headers
- **Content-Type:** Data format (application/json)
- **Date:** Response timestamp
- **Connection:** Connection type
- **Encoding:** Character encoding

### 4. Response Metrics
- **Response Time:** How long request took
- **Response Size:** Amount of data received
- **Cookies:** Temporary files for session management

## Best Practices

### Organization
1. **Use Descriptive Names** for collections and requests
2. **Group Related Requests** in same collection
3. **Create Folders** within collections for better organization
4. **Save Requests** after creating them

### Testing Strategy
1. **Start with GET requests** to understand API structure
2. **Test POST requests** to create test data
3. **Use PUT/PATCH** to modify existing data
4. **Clean up with DELETE** requests

### Collaboration
1. **Export Collections** to share with team members
2. **Use Team Workspaces** for collaborative projects
3. **Document API behavior** in request descriptions
4. **Version Control** collections through export/import

## Common Operations Summary

### Workspace Management
- Create → Workspaces → Create Workspace
- Rename → Click workspace name → Edit
- Delete → Workspace Settings → Delete Workspace

### Collection Management
- Create → Collections → Create Collection
- Run → Three dots → Run Collection
- Export → Three dots → Export
- Import → Import button → Select file

### Request Management
- Create → Add a request or + button
- Save → Save button after configuration
- Execute → Send button
- Organize → Drag and drop in collections

## Next Steps

In the next module, we will cover:
1. Creating custom APIs for testing
2. Advanced request configuration
3. Response validation techniques
4. Environment variables and data management
5. Automated testing with Postman

## Practice Exercises

1. **Setup Practice:**
   - Install Postman
   - Create your first workspace
   - Create a collection named "API Practice"

2. **Request Practice:**
   - Create GET request to `https://reqres.in/api/users?page=1`
   - Create POST request to create a new user
   - Create PUT request to update the user
   - Create DELETE request to remove the user

3. **Collection Management:**
   - Export your collection
   - Delete the collection
   - Import it back

Remember: Practice these basic operations before moving to advanced topics. Understanding workspaces, collections, and basic requests is fundamental to effective API testing with Postman.