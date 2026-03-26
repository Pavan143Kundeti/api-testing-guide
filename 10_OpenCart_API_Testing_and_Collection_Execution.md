# 10 OpenCart API Testing and Collection Execution

## OpenCart E-commerce API Testing

OpenCart is a popular e-commerce application that uses APIs for all operations. To test these APIs, we need to set up the application locally and create API users.

### Prerequisites Setup

#### 1. XAMPP Installation
XAMPP provides Apache, MySQL, and PHP components needed for OpenCart:

**Download XAMPP:**
- Recommended version: 7.4.29 (for compatibility)
- Avoid latest versions due to compatibility issues
- Install and start Apache and MySQL services

#### 2. OpenCart Installation
**Download OpenCart:**
- Recommended version: 3.0.3.8
- Extract to XAMPP's htdocs folder
- Rename config files from `config-dist.php` to `config.php`

#### 3. Database Setup
1. Access phpMyAdmin: `http://localhost/phpmyadmin`
2. Create database: "openshop"
3. Run OpenCart installation wizard
4. Delete install folder after completion

### Creating API User

#### Access Admin Panel
```
URL: http://localhost/opencart/upload/admin
Login: Use credentials from installation
```

#### Create API User Steps
1. Go to **System** → **Users** → **API**
2. Click **Add New** (+) button
3. Enter username (e.g., "demo")
4. Click **Generate** to create API key
5. Set status to **Enabled**
6. Add IP address in **IP Addresses** tab
7. Save configuration

**Important:** Save the API key - you'll need it for testing.

## OpenCart Cart API Testing

### API Workflow Overview

The OpenCart Cart API follows this workflow:

```
1. Create Session/Token → 2. Add Product to Cart → 3. Get Cart Content → 4. Edit Cart → 5. Remove Product
```

### Required Variables

| Variable | Type | Purpose |
|----------|------|---------|
| `ip` | Collection | Your local IP address |
| `baseUrl` | Collection | Common URL part |
| `api_token` | Collection | Authentication token |
| `product_id` | Collection | Product identifier |
| `quantity` | Collection | Product quantity |
| `cart_id` | Environment | Cart identifier |

### 1. Create Session/Token API

#### Purpose
Generate authentication token for other API requests.

#### Request Details
- **Method:** POST
- **Endpoint:** `/api/login`
- **Body Type:** form-data

#### Pre-request Script
```javascript
// Set base variables
pm.collectionVariables.set("ip", "192.168.1.100"); // Your IP
pm.collectionVariables.set("baseUrl", "http://{{ip}}/opencart/upload/index.php?route=");
```

#### Request Body (form-data)
```
username: demo
key: your_generated_api_key_here
```

#### Test Script
```javascript
pm.test("Status code is 200", () => {
    pm.response.to.have.status(200);
});

pm.test("Session created successfully", () => {
    const jsonData = pm.response.json();
    
    // Validate success message
    pm.expect(jsonData.success).to.equal("Success: API session successfully started!");
    
    // Store token for other requests
    pm.collectionVariables.set("api_token", jsonData.api_token);
});
```

#### Expected Response
```json
{
  "success": "Success: API session successfully started!",
  "api_token": "generated_token_value"
}
```

### 2. Add Product to Cart API

#### Purpose
Add a product to the shopping cart.

#### Request Details
- **Method:** POST
- **Endpoint:** `/api/cart/add`
- **Authorization:** API Key (query parameter)

#### Authorization
- **Type:** API Key
- **Key:** `api_token`
- **Value:** `{{api_token}}`
- **Add to:** Query Params

#### Pre-request Script
```javascript
// Generate product data
pm.collectionVariables.set("product_id", "40"); // Use valid product ID
pm.collectionVariables.set("quantity", "2");
```

#### Request Body (form-data)
```
product_id: {{product_id}}
quantity: {{quantity}}
```

#### Test Script
```javascript
pm.test("Status code is 200", () => {
    pm.response.to.have.status(200);
});

pm.test("Product added to cart", () => {
    const jsonData = pm.response.json();
    pm.expect(jsonData.success).to.include("Success: You have modified your shopping cart!");
});
```

### 3. Get Cart Content API

#### Purpose
Display products currently in the cart.

#### Request Details
- **Method:** POST
- **Endpoint:** `/api/cart/products`
- **Authorization:** API Key (query parameter)

#### Request Body (form-data)
```
product_id: {{product_id}}
```

#### Test Script
```javascript
pm.test("Status code is 200", () => {
    pm.response.to.have.status(200);
});

pm.test("Cart content retrieved", () => {
    const jsonData = pm.response.json();
    
    // Validate product information
    pm.expect(jsonData.products).to.be.an('array');
    pm.expect(jsonData.products.length).to.be.above(0);
    
    // Extract cart ID for next requests
    const cartId = jsonData.products[0].cart_id;
    pm.environment.set("cart_id", cartId);
});
```

#### Expected Response
```json
{
  "products": [
    {
      "cart_id": "7",
      "product_id": "40",
      "name": "iPhone",
      "quantity": "2",
      "price": "$123.20",
      "total": "$246.40"
    }
  ]
}
```

### 4. 

## Running Collections in Different Ways

After creating and testing your API collection, you need to run it in different environments. There are several ways to execute your Postman collections.

### Method 1: Run Collection Locally (Command Line)

#### Prerequisites
Before running collections from command line, you need:

1. **Node.js** - JavaScript runtime environment
2. **npm** - Node Package Manager (comes with Node.js)
3. **Newman** - Command line tool for Postman
4. **Newman HTML Reporter** - For generating HTML reports

#### Check if Node.js is Installed
Open command prompt and run:
```bash
node -v
npm -v
```

If you see version numbers, Node.js and npm are installed.

#### Install Newman
Run this command in command prompt:
```bash
npm install -g newman
```

#### Install HTML Reporter
Run this command to generate HTML reports:
```bash
npm install -g newman-reporter-html
```

#### Export Your Collection
1. In Postman, click on your collection
2. Click three dots → Export
3. Select recommended version
4. Save the JSON file to your computer

#### Run Collection Without Report
Navigate to folder where you saved the collection file:
```bash
newman run YourCollectionName.json
```

#### Run Collection With HTML Report
```bash
newman run YourCollectionName.json -r html
```

This creates a "newman" folder with HTML report inside.

### Method 2: Run Collection Remotely (Using URL)

Instead of using local files, you can run collections using shared URLs.

#### Share Your Collection
1. Click on collection → three dots → Share
2. Select "Via JSON Link"
3. Click "Update Link" to generate URL
4. Copy the generated URL

#### Run Shared Collection
```bash
newman run "https://your-shared-collection-url" -r html
```

Anyone with this URL can run your collection on their machine.

### Method 3: Run Collection in Jenkins

Jenkins is a tool for continuous integration. You can run your collections automatically in Jenkins.

#### Start Jenkins
If you have Jenkins installed:
```bash
java -jar jenkins.war
```

Access Jenkins at: `http://localhost:8080`

#### Create New Jenkins Project
1. Click "New Item"
2. Enter project name: "OpenCart API Testing"
3. Select "Freestyle project"
4. Click OK

#### Configure Jenkins Project
1. Go to "Build" section
2. Add "Execute Windows batch command" (or "Execute shell" for Mac/Linux)
3. Enter command:
```bash
newman run "your-collection-url" -r html
```
4. Save configuration

#### Run Project
1. Click "Build Now"
2. Check console output for results
3. HTML report will be generated in Jenkins workspace

### Method 4: Run Collection from GitHub

You can store your collections in GitHub and run them from there.

#### Push Collection to GitHub

**Step 1: Create GitHub Repository**
1. Go to GitHub.com
2. Click "New" repository
3. Name it "api-testing-collections"
4. Create repository

**Step 2: Create Local Git Repository**
Navigate to your collection folder and run:
```bash
git init
git remote add origin https://github.com/yourusername/api-testing-collections.git
```

**Step 3: Add and Commit Files**
```bash
git add YourCollection.json
git commit -m "Add OpenCart API collection"
```

**Step 4: Push to GitHub**
```bash
git push -u origin master
```

#### Run Collection from GitHub in Jenkins

**Configure Jenkins Project:**
1. Create new Jenkins project
2. In "Source Code Management", select "Git"
3. Enter your GitHub repository URL
4. In "Build" section, add command:
```bash
newman run YourCollection.json -r html
```

## Collection Execution Best Practices

### 1. Environment Management
- Use different environments for development, testing, production
- Store sensitive data in environment variables
- Never hardcode passwords or API keys

### 2. Error Handling
- Add proper test validations
- Handle authentication failures
- Check for network timeouts

### 3. Reporting
- Always generate HTML reports for better visibility
- Include test results in CI/CD pipelines
- Share reports with team members

### 4. Maintenance
- Update collections when APIs change
- Review and update test cases regularly
- Keep documentation up to date

## Troubleshooting Common Issues

### Newman Installation Issues
**Problem:** Newman command not found
**Solution:** 
- Reinstall Newman: `npm install -g newman`
- Check if Node.js is properly installed
- Restart command prompt

### Collection Execution Failures
**Problem:** Collection fails to run
**Solution:**
- Check if all required variables are set
- Verify API endpoints are accessible
- Check authentication credentials

### Report Generation Issues
**Problem:** HTML report not generated
**Solution:**
- Install HTML reporter: `npm install -g newman-reporter-html`
- Use correct command: `newman run collection.json -r html`
- Check file permissions

### Jenkins Integration Issues
**Problem:** Jenkins cannot run Newman
**Solution:**
- Install Node.js on Jenkins server
- Install Newman globally on Jenkins server
- Check Jenkins user permissions

## Summary

In this module, you learned:

1. **OpenCart Setup** - How to install and configure OpenCart locally
2. **API User Creation** - Creating API users in OpenCart admin panel
3. **API Testing Flow** - Understanding the complete workflow
4. **Collection Creation** - Building comprehensive API test collections
5. **Variable Management** - Using collection and environment variables
6. **API Chaining** - Connecting multiple API requests
7. **Collection Execution** - Running collections in multiple ways

### Key Takeaways

- **Local Installation Required** - OpenCart APIs need local installation for full access
- **Authentication Important** - All APIs require proper token authentication
- **Chaining Essential** - Real-world scenarios involve multiple connected API calls
- **Multiple Execution Methods** - Collections can run locally, remotely, or in CI/CD
- **Documentation Crucial** - Always refer to API documentation before testing

### Next Steps

1. **Practice** - Set up OpenCart and create your own API collections
2. **Explore** - Try different API endpoints and scenarios
3. **Automate** - Integrate collections into your CI/CD pipeline
4. **Share** - Collaborate with team members using shared collections
5. **Monitor** - Set up regular collection runs for API monitoring

This completes the comprehensive guide to OpenCart API testing and collection execution. You now have the knowledge to test real-world e-commerce APIs and run collections in various environments.

## Practice Exercises

### Exercise 1: Complete OpenCart Setup
1. Install XAMPP and OpenCart locally
2. Create API user with proper permissions
3. Test basic API endpoints manually

### Exercise 2: Build Complete Collection
1. Create collection with all 5 API requests
2. Implement proper variable management
3. Add comprehensive test validations
4. Test the complete workflow

### Exercise 3: Collection Execution
1. Export your collection
2. Run it using Newman command line
3. Generate HTML reports
4. Share collection URL with others

### Exercise 4: CI/CD Integration
1. Push collection to GitHub
2. Set up Jenkins project
3. Configure automated execution
4. Review generated reports

Remember: Real-world API testing requires understanding business workflows, proper authentication handling, and comprehensive validation strategies. Practice with OpenCart to build these essential skills.