# Student Management System
## Complete JDBC Project with Full CRUD Operations

---

## 📚 PROJECT OVERVIEW

**Project Name:** Student Management System  
**Technology Stack:** Java, JDBC, MySQL, HTML5, CSS3, JavaScript  
**Architecture:** 3-Tier (Presentation, Business Logic, Data Access)  
**Database:** MySQL with 2 tables (students, courses)  
**Port:** 8080 (web) or console-based  

---

## 🎯 PROJECT FEATURES

### **CRUD Operations - Complete Implementation**

| Operation | Method | Endpoint | Status |
|-----------|--------|----------|--------|
| **CREATE** | addStudent() | POST /addStudent | ✅ |
| **READ** | getAllStudents() | GET /getAllStudents | ✅ |
| **READ** | getStudent() | GET /getStudent?id= | ✅ |
| **READ** | searchStudents() | GET /searchStudents?k= | ✅ |
| **UPDATE** | updateStudent() | POST /updateStudent | ✅ |
| **DELETE** | deleteStudent() | POST /deleteStudent?id= | ✅ |
| **DELETE** | deleteAllStudents() | POST /deleteAllStudents | ✅ |
| **STATS** | getStatistics() | GET /getStatistics | ✅ |

### **Additional Features**

✅ Real-time statistics (Total, Average GPA, Highest GPA)  
✅ Student search by name or email  
✅ Form validation  
✅ Responsive web design  
✅ Beautiful UI with gradients  
✅ Console-based application option  
✅ Error handling & logging  
✅ Database connection testing  

---

## 📂 PROJECT STRUCTURE

```
StudentManagement/
├── src/com/student/management/
│   ├── StudentDAO.java              (Data Access Object - JDBC)
│   ├── StudentHandler.java          (HTTP Request Handler)
│   ├── StudentManagementApp.java    (Console Application)
│   └── StudentManagementWebApp.java (Web Server Main)
│
├── web/
│   ├── index.html                   (Web Interface)
│   ├── style.css                    (Styling)
│   └── script.js                    (Frontend Logic)
│
├── database.sql                     (Database Setup)
└── README.md                        (Documentation)
```

---

## 🗄️ DATABASE DESIGN

### **Database: student_management**

#### **Table 1: students**

```sql
CREATE TABLE students (
    id INT PRIMARY KEY AUTO_INCREMENT,
    name VARCHAR(150) NOT NULL,
    email VARCHAR(100) NOT NULL UNIQUE,
    phone VARCHAR(15) NOT NULL,
    address VARCHAR(255) NOT NULL,
    enrollment_date DATE NOT NULL,
    gpa DECIMAL(3,2) NOT NULL,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP
);
```

**Fields:**
- `id` - Unique student identifier
- `name` - Student full name
- `email` - Email address (unique constraint)
- `phone` - Contact number
- `address` - Student address
- `enrollment_date` - Date of enrollment
- `gpa` - Grade Point Average (0.00 - 4.00)
- `created_at` - Record creation timestamp
- `updated_at` - Last update timestamp

---

#### **Table 2: courses**

```sql
CREATE TABLE courses (
    id INT PRIMARY KEY AUTO_INCREMENT,
    course_name VARCHAR(150) NOT NULL,
    course_code VARCHAR(20) NOT NULL UNIQUE,
    credits INT NOT NULL,
    instructor VARCHAR(150) NOT NULL,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```

---

## 💻 JAVA IMPLEMENTATION

### **1. StudentDAO.java - Data Access Object**

**Purpose:** Handle all database operations using JDBC

**Key Methods:**

#### **CREATE - Add Student**
```java
public boolean addStudent(String name, String email, String phone, 
                         String address, String enrollmentDate, double gpa) 
    throws SQLException
```
- Uses `PreparedStatement` to prevent SQL injection
- Binds parameters safely with `setString()`, `setDouble()`
- Executes INSERT query
- Returns boolean success status

#### **READ - Get All Students**
```java
public List<Map<String, Object>> getAllStudents() throws SQLException
```
- Executes SELECT query
- Iterates through ResultSet
- Maps each row to HashMap
- Returns List of student maps
- ORDER BY id DESC (newest first)

#### **READ - Search Students**
```java
public List<Map<String, Object>> searchStudents(String keyword) throws SQLException
```
- Uses LIKE operator for flexible search
- Searches name and email fields
- Returns matching students only

#### **READ - Get Statistics**
```java
public int getTotalStudents() throws SQLException
public double getAverageGPA() throws SQLException
```
- Uses SQL aggregate functions: COUNT(), AVG()
- Calculates database-level statistics

#### **UPDATE - Modify Student**
```java
public boolean updateStudent(int id, String name, String email, 
                            String phone, String address, double gpa) 
    throws SQLException
```
- Updates specific student by ID
- Uses WHERE clause to target record
- All fields are updateable

#### **DELETE - Remove Student**
```java
public boolean deleteStudent(int id) throws SQLException
```
- Deletes student by ID
- Irreversible operation

### **JDBC Connection Management**

```java
private static final String URL = "jdbc:mysql://localhost:3306/student_management";
private static final String USER = "root";
private static final String PASSWORD = "root";

// Establish connection
Connection conn = DriverManager.getConnection(URL, USER, PASSWORD);

// Use try-with-resources for automatic cleanup
try (Connection conn = DriverManager.getConnection(URL, USER, PASSWORD);
     PreparedStatement pstmt = conn.prepareStatement(sql)) {
    // Use connection and statement
} // Auto-closed here
```

**Key Points:**
- `DriverManager.getConnection()` - Establishes connection
- Try-with-resources - Automatically closes Connection & Statement
- `PreparedStatement` - Safe parameter binding
- `ResultSet` - Iterate through query results

---

### **2. StudentHandler.java - HTTP Request Handler**

**Purpose:** Handle incoming HTTP requests and route to DAO methods

**Request Routing:**

```java
public void handle(HttpExchange exchange) throws IOException {
    String path = exchange.getRequestURI().getPath();
    String method = exchange.getRequestMethod();
    
    switch(path) {
        case "/": return serveFile(exchange, "web/index.html");
        case "/addStudent": return addStudent(exchange);
        case "/getAllStudents": return getAllStudents(exchange);
        case "/getStudent": return getStudent(exchange);
        // ... more endpoints
    }
}
```

**Request/Response Flow:**

```
HTTP Request → StudentHandler.handle()
    ↓
Parse request parameters
    ↓
Call appropriate StudentDAO method
    ↓
Get result from database
    ↓
Convert to JSON format
    ↓
Send HTTP Response (application/json)
    ↓
JavaScript receives and processes
    ↓
Update web page dynamically
```

---

### **3. StudentManagementApp.java - Console Application**

**Purpose:** Provide command-line interface for CRUD operations

**Menu Options:**
```
1. Add New Student
2. View All Students
3. Search Student
4. Update Student Information
5. Delete Student
6. View Statistics
7. Exit
```

**Usage:**
```bash
javac StudentManagementApp.java StudentDAO.java
java StudentManagementApp
```

---

### **4. StudentManagementWebApp.java - Web Server**

**Purpose:** Start HTTP server for web interface

```java
HttpServer server = HttpServer.create(new InetSocketAddress(8080), 0);
server.createContext("/", new StudentHandler());
server.setExecutor(Executors.newFixedThreadPool(10));
server.start();
```

**Usage:**
```bash
javac *.java
java StudentManagementWebApp
# Open http://localhost:8080
```

---

## 🌐 WEB INTERFACE

### **HTML Structure (index.html)**

```
┌─────────────────────────────────┐
│         Header                  │
│ Student Management System       │
└─────────────────────────────────┘
┌──────────────┬────────┬──────────┐
│ Total: 8     │ Avg GP │ Highest │
│ Students     │ A: 3.7 │ 3.9     │
└──────────────┴────────┴──────────┘
┌─────────────────────────────────┐
│ Add | Search | Update Tabs      │
│ Form with inputs                │
│ Submit Button                   │
└─────────────────────────────────┘
┌─────────────────────────────────┐
│ Student Records Table           │
│ ID | Name | Email | ... | Action│
│ ...                             │
└─────────────────────────────────┘
```

### **CSS Styling (style.css)**

- **Color Scheme:** Purple (#667eea) & Blue (#764ba2) gradients
- **Layout:** Responsive grid for statistics
- **Animations:** Smooth transitions on hover
- **Typography:** Modern sans-serif fonts
- **Mobile:** Responsive design with media queries

### **JavaScript (script.js)**

**Key Functions:**

```javascript
// Load all students and populate table
loadAllStudents()

// Load statistics (total, avg, highest)
loadStatistics()

// Switch between form tabs
switchTab(tabName)

// Add new student via API
addStudent() // fetch POST /addStudent

// Search students
searchStudents() // fetch GET /searchStudents?keyword=

// Update student info
updateStudent() // fetch POST /updateStudent

// Delete student
deleteStudent(id) // fetch POST /deleteStudent?id=

// Get single student for editing
loadStudentForUpdate()
```

---

## 🔒 SECURITY FEATURES

### **1. SQL Injection Prevention**

❌ **Vulnerable:**
```java
String sql = "SELECT * FROM students WHERE id = " + userId;
```

✅ **Safe:**
```java
String sql = "SELECT * FROM students WHERE id = ?";
PreparedStatement pstmt = conn.prepareStatement(sql);
pstmt.setInt(1, userId);
```

**How PreparedStatement works:**
- Separates SQL code from data
- Parameters are properly escaped
- Database treats them as data, not code

### **2. Connection Management**

```java
try (Connection conn = DriverManager.getConnection(...);
     PreparedStatement pstmt = conn.prepareStatement(sql)) {
    // Resources automatically closed
}
```

### **3. Input Validation**

- Email uniqueness constraint in database
- GPA range validation (0-4)
- Phone format validation (HTML5)
- Required field validation

---

## 📊 SAMPLE DATA

### **8 Sample Students**

| ID | Name | Email | GPA |
|----|------|-------|-----|
| 1 | Ramesh Kumar | ramesh@example.com | 3.8 |
| 2 | Priya Singh | priya@example.com | 3.9 |
| 3 | Arjun Poudel | arjun@example.com | 3.5 |
| ... | ... | ... | ... |

### **5 Sample Courses**

| ID | Course Name | Code | Credits |
|----|------------|------|---------|
| 1 | Data Structures | CS-101 | 4 |
| 2 | DBMS | CS-201 | 4 |
| ... | ... | ... | ... |

---

## 🚀 INSTALLATION & SETUP

### **Prerequisites**

- Java JDK 11+
- MySQL Server
- MySQL JDBC Driver (mysql-connector-j.jar)
- IDE: Eclipse, STS, or IntelliJ

### **Step-by-Step Setup**

#### **1. Database Setup**

```bash
mysql -u root -p
```

```sql
-- Run database.sql
source StudentManagement_database.sql;

-- Verify
USE student_management;
SHOW TABLES;
SELECT COUNT(*) FROM students;
```

#### **2. Java Project Setup**

```bash
# Create project directory
mkdir StudentManagement
cd StudentManagement

# Create source directory
mkdir -p src/com/student/management
mkdir web

# Copy Java files to src/com/student/management/
# Copy web files to web/ directory
```

#### **3. Add MySQL JAR**

- Download: [mysql-connector-j.jar](https://dev.mysql.com/downloads/connector/j/)
- Add to project Build Path / Classpath

#### **4. Compile & Run**

```bash
# Console Version
javac src/com/student/management/*.java
java -cp src com.student.management.StudentManagementApp

# Web Version
java -cp src com.student.management.StudentManagementWebApp
# Open: http://localhost:8080
```

---

## 📖 API DOCUMENTATION

### **POST /addStudent**

**Request:**
```
name=John&email=john@example.com&phone=9841234567
&address=Kathmandu&enrollmentDate=2024-01-15&gpa=3.8
```

**Response:**
```json
{"status":"success","message":"Student added"}
```

### **GET /getAllStudents**

**Response:**
```json
[
  {
    "id": 1,
    "name": "Ramesh Kumar",
    "email": "ramesh@example.com",
    "phone": "9841234567",
    "address": "Kathmandu",
    "enrollment_date": "2023-01-15",
    "gpa": 3.8
  },
  ...
]
```

### **GET /getStudent?id=1**

**Response:**
```json
{
  "id": 1,
  "name": "Ramesh Kumar",
  "email": "ramesh@example.com",
  "phone": "9841234567",
  "address": "Kathmandu",
  "gpa": 3.8
}
```

### **GET /searchStudents?keyword=ramesh**

**Response:**
```json
[
  {
    "id": 1,
    "name": "Ramesh Kumar",
    "email": "ramesh@example.com",
    "gpa": 3.8
  }
]
```

### **POST /updateStudent**

**Request:**
```
id=1&name=Ram Kumar&email=ram@example.com
&phone=9842222222&address=Pokhara&gpa=3.9
```

### **POST /deleteStudent?id=1**

**Response:**
```json
{"status":"success","message":"Student deleted"}
```

### **GET /getStatistics**

**Response:**
```json
{
  "totalStudents": 8,
  "averageGPA": 3.72,
  "highestGPA": 3.9
}
```

---

## 🎓 LEARNING OUTCOMES

### **1. JDBC Fundamentals**
- ✅ Connection management
- ✅ PreparedStatement usage
- ✅ ResultSet processing
- ✅ Exception handling
- ✅ Resource cleanup (try-with-resources)

### **2. CRUD Operations**
- ✅ INSERT (CREATE new records)
- ✅ SELECT (READ existing records)
- ✅ UPDATE (MODIFY records)
- ✅ DELETE (REMOVE records)

### **3. DAO Pattern**
- ✅ Data Access Object design pattern
- ✅ Business logic separation
- ✅ Reusable database operations
- ✅ Clean code architecture

### **4. HTTP Server**
- ✅ Java HttpServer API
- ✅ Request handling
- ✅ Response generation
- ✅ Static file serving

### **5. Web Development**
- ✅ RESTful API design
- ✅ JSON data format
- ✅ Fetch API (async requests)
- ✅ DOM manipulation
- ✅ Form handling
- ✅ Responsive design

### **6. Database Design**
- ✅ Table structure planning
- ✅ Constraints (PRIMARY KEY, UNIQUE)
- ✅ Data types selection
- ✅ Timestamp management

---

## 🛠️ TROUBLESHOOTING

| Problem | Solution |
|---------|----------|
| "Connection refused" | Ensure MySQL is running: `mysql.server start` |
| "Unknown database" | Run database.sql: `source StudentManagement_database.sql;` |
| "Class not found" | Add MySQL JAR to classpath |
| "Port already in use" | Change port in StudentManagementWebApp.java |
| "NaN in statistics" | Check database connection and table data |
| 404 on /style.css | Ensure web/ folder exists with files |

---

## 📈 PERFORMANCE CONSIDERATIONS

- **Connection Pooling:** Use HikariCP for multiple connections
- **Indexing:** Add indexes on frequently searched columns
- **Pagination:** Implement for large result sets
- **Caching:** Cache statistics using Redis
- **Load Balancing:** Use Nginx for multiple server instances

---

## 🔗 PROJECT VARIANTS

This project includes:

### **Option 1: Console Application**
- StudentManagementApp.java
- Menu-driven interface
- Direct user input
- No web server needed

### **Option 2: Web Application**
- StudentManagementWebApp.java
- HTML/CSS/JavaScript interface
- RESTful API
- Browser-based access

### **Option 3: DAO-Only**
- StudentDAO.java
- Can be integrated with Spring Boot
- Standalone data access layer
- Reusable in other projects

---

## 📝 CONCLUSION

This Student Management System is a **comprehensive JDBC project** demonstrating:
- ✅ Professional JDBC usage
- ✅ Complete CRUD implementation
- ✅ Clean architecture with DAO pattern
- ✅ Responsive web interface
- ✅ RESTful API design
- ✅ Security best practices
- ✅ Error handling & validation

Perfect for learning Java database programming and building production-ready applications!

---

**Project Status:** ✅ Complete & Production Ready  
**Version:** 1.0  
**Last Updated:** 2026
