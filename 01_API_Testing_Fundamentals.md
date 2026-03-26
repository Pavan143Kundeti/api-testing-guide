# 01 API Testing Fundamentals

## What is Software?
- Software is an application developed for specific clients or customers
- Software can also be developed based on market requirements (called products)
- We need to release quality software to customers

## Types of Testing
- Web testing
- API testing  
- Mobile app testing
- Performance testing
- Security testing

## Client and Server Basics

### What is Client?
- Client is where we access the application from
- Examples: Browser, mobile apps
- Client is a computer or device that accesses services from a server
- We are all clients when we browse websites through browsers

### What is Server?
- Server contains the actual application or software
- Server is where web pages and data actually exist
- Servers are often in remote locations
- We access servers through URLs and internet connection

### How Client-Server Works
1. Client sends request to server (example: typing google.com)
2. Server processes the request
3. Server sends response back to client
4. Client displays the response (web pages, data)

## Application Architecture Types

### 1-Tier Architecture
- Client and server on same machine
- Data saved in file format (like notepad, MS Word)
- No internet needed
- Pretty old technology, not used much today

### 2-Tier Architecture  
- Multiple clients access single database server
- Database server is on different machine
- Example: Bank employees accessing same database
- Used in local organizations

### 3-Tier Architecture
- Most web applications use this
- Three layers:
  1. **Presentation Layer** (Client/UI)
  2. **Application Layer** (Business Logic/Web Server)  
  3. **Data Layer** (Database)

## Three-Tier Architecture Details

| Layer | Also Called | Purpose | Technologies |
|-------|-------------|---------|--------------|
| Presentation Layer | Client Layer, UI Layer | Present data to user | HTML, JavaScript, CSS |
| Application Layer | Business Logic, Web Server | Process requests, contain APIs | Java, .NET, C#, Python, C++ |
| Data Layer | Database Layer | Store actual data | MySQL, Oracle, SQL Server, MongoDB |

### How Three Layers Work Together
1. User interacts with Presentation Layer (UI)
2. Request goes to Application Layer (contains business logic/APIs)
3. Application Layer sends request to Data Layer (database)
4. Data Layer fetches data and sends back to Application Layer
5. Application Layer sends response to Presentation Layer
6. User sees the result

## What is API?

### API Definition
- **API** = Application Programming Interface
- API is used for communication between two applications
- API acts as mediator between different applications
- Applications may use different platforms or technologies

### Where APIs Live
- APIs are present in the **Application Layer** (middle layer)
- APIs contain business logic developed by programmers
- APIs take requests from presentation layer
- APIs send requests to database layer
- APIs send responses back to presentation layer

### Restaurant Example
To understand API role:

| Role | In Restaurant | In Software |
|------|---------------|-------------|
| Client | Customer | User/Browser |
| API | Waiter | Application Programming Interface |
| Server | Chef/Kitchen | Database |

**Process:**
1. Customer orders food (Client sends request)
2. Waiter takes order to kitchen (API forwards request to database)
3. Chef prepares food (Database processes request)
4. Waiter brings food to customer (API sends response to client)

## Real-World API Examples

### MakeMyTrip Example
- MakeMyTrip website uses different airline APIs
- When you search flights, MakeMyTrip sends request to airline APIs
- Airlines have their own APIs with flight information
- MakeMyTrip gets responses from multiple airline APIs
- Shows combined results to user

### Google Maps API
- Google developed Google Maps APIs
- Other apps use Google Maps through APIs
- Examples: Uber, WhatsApp location sharing
- Apps don't develop their own maps
- They use Google's APIs to get map functionality

### Google Account Login
- You can login to Facebook using Google account
- Facebook requests Google through API to verify your account
- If valid, Google API responds positively
- Facebook allows you to login
- Same process for LinkedIn and other apps

## API vs Web Service

### API (Development/Testing Phase)
- APIs during development and testing
- No internet required for development/testing
- Can test APIs locally
- Called just "APIs"

### Web Service (Production Phase)  
- APIs moved to internet/production
- Available for public access
- Requires network/internet
- Called "Web Services"

### Key Points
- All web services are APIs
- Not all APIs are web services
- APIs become web services when moved to production
- Web services need network, APIs don't need network for operation

## Types of APIs

### 1. SOAP APIs
- Simple Object Access Protocol
- Very old technology
- Uses only XML format
- XML is lengthy and complex
- Very few companies still use it

### 2. REST APIs  
- Representational State Transfer
- Latest technology
- Most companies (80%) use REST APIs
- Supports multiple data formats: XML, JSON, HTML, text
- We will focus on REST APIs in this course

## REST API Methods

REST APIs support different HTTP request methods:

| Method | Purpose | Example |
|--------|---------|---------|
| GET | Receive/fetch data from server | Browse google.com, search results |
| POST | Send/create data in server | User registration, create new record |
| PUT | Update existing data in server | Edit profile information |
| DELETE | Delete data from server | Remove user account |

### HTTP vs HTTPS
- **HTTP**: Hypertext Transfer Protocol
- **HTTPS**: Hypertext Transfer Protocol Secure
- HTTPS is more secure (encrypted data)
- HTTP sends data in original format (less secure)
- HTTPS encrypts data during transmission

## Important API Terminology

### URL Components
Example: `https://google.com/articles/article-name`

| Component | Name | Description |
|-----------|------|-------------|
| https | Protocol/Scheme | Communication method |
| google.com | Host/Domain | Where data is located |
| /articles/article-name | Path | Location of specific resource |

### Key Terms

**URI/URL**
- URI = Uniform Resource Identifier  
- URL = Uniform Resource Locator
- Both terms often used interchangeably
- Complete web address

**Endpoint**
- Path part of URL (excluding host)
- Example: `/articles/article-name`
- Provided by developers
- Doesn't change when APIs move to different servers

**Resource**
- Actual data/functionality available on server
- What we request from API
- Located using the path/endpoint

**Payload**
- Data sent with request = Request Payload
- Data received in response = Response Payload
- Examples: User details, flight booking information

**Consumer/Producer**
- Consumer = Client sending requests
- Producer = Server providing responses

## API Testing Benefits

### Why Test APIs?
1. **Early Testing**: APIs ready before UI development
2. **Time Saving**: 80% testing at API level, 20% at UI level  
3. **Faster Development**: No need to wait for UI completion
4. **Better Coverage**: Test core functionality directly
5. **Reduced Effort**: Less time needed for UI testing

### Development Sequence
1. Database development (first)
2. API development (second)  
3. Web/UI development (third)

### Testing Strategy
1. Test APIs as soon as they're ready
2. Complete 70-80% testing at API level
3. Do remaining 20% testing on UI (basic UI validation)
4. UI testing focuses on: alignment, colors, fonts, element positioning

## What We'll Learn

### Manual API Testing
- Tool: **Postman**
- Learn request/response structure
- Understand different HTTP methods
- Practice with real APIs
- Learn to validate responses

### Automated API Testing
- Tool: **REST Assured** (Java)
- Write test scripts for APIs
- Automate repetitive testing tasks
- Integration with testing frameworks
- Continuous testing in CI/CD pipelines

## API Testing Tools

### Popular Manual Testing Tools
1. **Postman** - Most popular, user-friendly interface
2. **Insomnia** - Lightweight alternative to Postman
3. **Thunder Client** - VS Code extension
4. **Advanced REST Client** - Chrome extension

### Popular Automation Tools
1. **REST Assured** - Java-based framework
2. **Requests** - Python library
3. **Axios/Fetch** - JavaScript libraries
4. **Newman** - Command-line runner for Postman

## Getting Started with API Testing

### Prerequisites
- Basic understanding of HTTP protocols
- Knowledge of JSON/XML formats
- Understanding of client-server architecture
- Familiarity with testing concepts

### Next Steps
1. Install Postman
2. Learn to create requests
3. Understand response validation
4. Practice with public APIs
5. Move to automation frameworks

## Summary

API testing is crucial for:
- Early defect detection
- Faster feedback cycles
- Better test coverage
- Reduced testing effort
- Quality assurance

APIs form the backbone of modern applications, making API testing an essential skill for testers and developers.