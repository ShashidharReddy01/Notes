# 1. Introduction to DBMS

## 1.1 What is Data

**Data** is raw, unprocessed facts and figures. It can be numbers, text, images, sounds, or any representation of information that has not been interpreted or given meaning.

- **Example:** The value "25" is data. We do not know if it represents age, temperature, or quantity until we add context.
- **Example:** "John", "Smith", "1990-05-15" are pieces of data that could describe a person when combined.

Data becomes **information** when it is processed, organized, and given context so that it becomes meaningful and useful for decision-making.

## 1.2 What is a Database

A **database** is an organized, persistent collection of related data stored in a structured format so that it can be easily accessed, managed, and updated.

- **Organized:** Data is stored in a logical structure (e.g., tables, documents).
- **Persistent:** Data remains even after the application or system is shut down.
- **Related:** Data items are connected (e.g., customers and their orders).

**Real-world example:** A library database stores books, authors, borrowers, and loan records. All are related and organized so librarians can find books, track who borrowed what, and manage returns.

## 1.3 What is DBMS

A **Database Management System (DBMS)** is software that enables users to define, create, maintain, and control access to a database. It acts as an interface between the database and the end users or application programs.

**In simple terms:** If the database is a filing cabinet, the DBMS is the system (and the person) that knows how to file, retrieve, and organize the documents and who is allowed to see or change them.

**Key functions of a DBMS:**

| Function | Description |
|----------|-------------|
| Data definition | Define structure (tables, columns, types) |
| Data manipulation | Insert, update, delete, query data |
| Data retrieval | Search and retrieve data efficiently |
| Data security | Control who can access or modify what |
| Data integrity | Enforce rules so data stays correct |
| Concurrency control | Allow multiple users without corruption |
| Backup and recovery | Restore data after failures |

**Popular DBMS examples:** MySQL, PostgreSQL, Oracle, Microsoft SQL Server, MongoDB (for document data).

## 1.4 Why DBMS is Needed

Without a DBMS, applications would have to:

- Store data in files and write their own code to read/write them.
- Handle multiple users updating the same file (risk of overwriting).
- Enforce business rules (e.g., "age must be positive") in every application.
- Implement backup, recovery, and security from scratch.

A DBMS centralizes these tasks so that:

1. **Data is shared** safely across many applications and users.
2. **Redundancy and inconsistency** can be minimized.
3. **Integrity and security** are enforced in one place.
4. **Development time** is reduced; applications focus on business logic.

## 1.5 File System vs DBMS

| Aspect | File System | DBMS |
|--------|-------------|------|
| Data redundancy | High; same data in multiple files | Low; data stored once, referenced |
| Data inconsistency | Possible; one file updated, another not | Reduced; single source of truth |
| Data sharing | Difficult; file locking, format issues | Built-in; multi-user access |
| Data integrity | Application must enforce | DBMS enforces constraints |
| Security | File-level or OS-level | User/role-based, fine-grained |
| Backup and recovery | Manual, application-specific | Built-in backup, logging, recovery |
| Concurrent access | Risk of corruption | Transactions and locking |
| Query capability | Programmatic only | Declarative (e.g., SQL) |

**Example:** In a file system, "Customer Name" might be stored in both `orders.txt` and `invoices.txt`. If the customer changes their name, both files must be updated; if one is missed, data is inconsistent. In a DBMS, the name is stored once in a Customers table and referenced by orders and invoices, so one update keeps everything consistent.

## 1.6 Basic Terminology

| Term | Meaning |
|------|---------|
| **Schema** | The structure of the database (tables, columns, types, constraints). "Blueprint" of the database. |
| **Instance** | The actual data stored in the database at a given time. Changes as data is inserted/updated/deleted. |
| **Table / Relation** | A set of rows (tuples) and columns (attributes) storing one type of entity (e.g., Employees). |
| **Row / Tuple / Record** | One horizontal entry in a table; represents one entity (e.g., one employee). |
| **Column / Attribute / Field** | One vertical component; represents one property (e.g., EmployeeName). |
| **Domain** | Set of allowed values for an attribute (e.g., Age: integers from 0 to 150). |
| **Key** | Attribute(s) used to uniquely identify a row (e.g., EmployeeID). |
| **Query** | A request to retrieve or modify data (e.g., "list all employees in Sales"). |
| **Transaction** | A logical unit of work (e.g., "transfer money from A to B") that must complete fully or not at all. |

---

# 2. Challenges in Database Systems

## 2.1 Data Redundancy

**Definition:** Data redundancy occurs when the same piece of data is stored in multiple places.

**Problems:**

- Wasted storage space.
- Update anomalies: when data is updated in one place but not in others, the database becomes inconsistent.
- Increased risk of errors during updates.

**Example:** If customer address is stored in both a "Customers" table and an "Orders" table, changing the address in Customers but not in old Orders leads to conflicting information. Normalization and centralizing data in one table reduce redundancy.

## 2.2 Data Inconsistency

**Definition:** Data inconsistency means different copies or representations of the same fact show different values.

**Causes:** Redundancy, partial updates, concurrent updates without proper control, or no single source of truth.

**Example:** One report says "Total Sales = 10000" and another says "Total Sales = 9500" for the same period because they read from different tables or snapshots that were not updated together. DBMS uses transactions and constraints to minimize this.

## 2.3 Data Integrity

**Definition:** Data integrity means data is accurate, complete, and conforms to defined rules.

**Types:**

- **Entity integrity:** Each row is uniquely identified (e.g., primary key not null and unique).
- **Referential integrity:** References between tables are valid (e.g., every Order has a valid CustomerID).
- **Domain integrity:** Values are within allowed sets (e.g., Age between 0 and 150).
- **User-defined rules:** Business rules (e.g., "End date must be after start date").

DBMS enforces these through primary keys, foreign keys, check constraints, and triggers.

## 2.4 Data Security

**Challenges:** Unauthorized access, theft, or modification of data. Requirements include:

- **Confidentiality:** Only authorized users see sensitive data.
- **Integrity:** Only authorized users can change data.
- **Availability:** Authorized users can access data when needed.

DBMS provides authentication (who you are), authorization (what you can do), encryption, and audit logging to address these.

## 2.5 Concurrent Access Problems

When multiple users access and modify the same data simultaneously:

- **Lost update:** Two updates overwrite each other; one is lost.
- **Dirty read:** One transaction reads uncommitted data from another; that transaction may roll back.
- **Non-repeatable read:** Same query in one transaction returns different results because another transaction changed the data.
- **Phantom read:** New rows appear (or disappear) between two reads in the same transaction.

DBMS uses transactions, isolation levels, and concurrency control (locking, timestamps, etc.) to avoid or limit these problems.

## 2.6 Scalability Challenges

As data volume and user count grow:

- **Storage:** More disk space and efficient storage structures (indexes, partitioning).
- **Performance:** Queries must remain fast (query optimization, indexing, caching).
- **Concurrency:** More users without degrading performance or correctness (connection pooling, efficient locking).
- **Availability:** System should stay up (replication, failover, distributed systems).

Design choices (schema, indexes, partitioning, replication) and DBMS features address these challenges.

---

# 3. Database Architecture

Database architecture describes how the DBMS and applications are organized in terms of tiers (layers) and how they communicate.

## 3.1 1-Tier Architecture

In **1-tier architecture**, the database and the application run on the same machine. The user interacts directly with the system where the database resides.

```
+------------------------------------------+
|  Single Machine                          |
|  +------------------+  +---------------+ |
|  |  Application     |  |  Database     | |
|  |  (UI + Logic)    |<->|  (DBMS+Data) | |
|  +------------------+  +---------------+ |
+------------------------------------------+
```

**Characteristics:**

- Simplest setup; no network between app and DB.
- Used for single-user or development scenarios (e.g., desktop app with embedded SQLite).
- Not suitable for multiple users or remote access in production.

**Example:** A small desktop inventory app running on one PC with a local database file.

## 3.2 2-Tier Architecture (Client-Server)

**2-tier architecture** has two layers: **client** (application + UI) and **server** (DBMS + database). The client sends requests (e.g., SQL) over the network; the server processes them and returns results.

```
+----------------+         Network        +----------------+
|  Client        |  <----------------->  |  Server        |
|  - UI          |    SQL / Results      |  - DBMS        |
|  - App Logic   |                       |  - Database    |
+----------------+                       +----------------+
```

**Characteristics:**

- Client handles presentation and business logic; server handles data storage and query processing.
- Direct connection from client to DB (e.g., ODBC/JDBC).
- Good for departmental or medium-sized applications; scaling means more clients or a more powerful server.

**Example:** A company application where each employee runs a client on their PC and all connect to one central database server.

## 3.3 3-Tier Architecture

**3-tier architecture** adds a middle tier between the client and the database:

1. **Presentation tier:** User interface (e.g., web browser, mobile app).
2. **Application/Business logic tier:** Server that implements business rules, validates input, and talks to the database (e.g., web server, API server).
3. **Data tier:** DBMS and database.

```
+-----------+      +-------------------+      +----------------+
|  Client   | <->  |  Application      | <->  |  Data Tier     |
|  (UI)     |      |  (Business Logic) |      |  (DBMS + DB)   |
+-----------+      +-------------------+      +----------------+
```

**Characteristics:**

- Clients do not connect directly to the database; they only talk to the application server. This improves security and allows reuse of business logic.
- Scaling can be done per tier (e.g., more app servers, separate DB server).
- Common in web and enterprise applications.

**Example:** A web store: browser (tier 1) sends requests to a web/API server (tier 2), which executes business logic and queries the database (tier 3).

## 3.4 Client-Server Architecture (General)

**Client-server** is a model where:

- **Server:** Provides services (e.g., database service). Waits for requests, processes them, and sends responses. Typically more powerful and centralized.
- **Client:** Requests services from the server. Can be many; often lighter (e.g., browser, mobile app).

In the 2-tier DB context, the DBMS runs on the server; clients are applications that connect to it. In 3-tier, the "server" from the user's perspective is often the application server, which in turn acts as a client to the database server.

## 3.5 Example Scenarios Summary

| Architecture | Example scenario |
|--------------|------------------|
| 1-Tier | Personal finance app with local SQLite on one laptop |
| 2-Tier | Office application: desktop clients connect to one Oracle/MySQL server |
| 3-Tier | E-commerce website: browser, Node.js/Java server, PostgreSQL database |

---

# 4. Data Models

A **data model** defines how data is structured, related, and constrained. It provides a set of concepts (entities, relationships, attributes) and rules for representing the real world in the database.

## 4.1 Hierarchical Model

**Concept:** Data is organized in a tree structure. Each "parent" can have many "children"; each child has exactly one parent.

**Structure:** Records (nodes) connected by parent-child links. Navigation is top-down (root to leaves) or from child to parent.

```
        [ROOT: Company]
       /       |       \
   [Dept A] [Dept B] [Dept C]
      |         |        |
  [Emp1][Emp2] [Emp3]  [Emp4]
```

**Example:** Organization chart: Company -> Departments -> Employees. To find an employee, you traverse from Company to Department to Employee.

**Limitation:** Representing many-to-many or multiple parents is awkward (e.g., an employee in multiple projects); data may be duplicated.

## 4.2 Network Model

**Concept:** Generalization of the hierarchical model. A record can have multiple parent and child records, forming a graph (network) of relationships.

**Structure:** Records (nodes) and sets (links). One record type can participate in multiple "owner-member" relationships.

```
    [Student] -----< enrolls in >---- [Course]
         |                                  |
         | has                              | has
         v                                  v
    [Grade]                            [Instructor]
```

**Example:** Students enroll in many courses; courses have many students. Students have many grades; courses have many grades. This many-to-many is natural in the network model.

**Limitation:** More flexible than hierarchical but complex to design and query; navigation is often procedural.

## 4.3 Relational Model

**Concept:** Data is stored in **relations (tables)**. Each table has rows (tuples) and columns (attributes). Relationships between entities are represented by storing key values in columns (e.g., foreign keys), not by physical links.

**Structure:** Tables, rows, columns. Keys (primary, foreign) enforce identity and relationships. Operations are based on set theory (relational algebra) and expressed in SQL.

**Example:**

- Table **Students:** StudentID, Name, DOB
- Table **Courses:** CourseID, Title, Credits
- Table **Enrollments:** StudentID, CourseID, Grade (links Students and Courses)

**Advantages:** Simple, declarative (SQL), strong theory, no physical navigation; dominates modern DBMS (Oracle, MySQL, PostgreSQL, SQL Server).

## 4.4 Entity-Relationship Model (ER Model)

**Concept:** A **conceptual** data model used for design. It describes the world in terms of **entities**, **attributes**, and **relationships**, and is often depicted as ER diagrams. It is then mapped to a logical model (e.g., relational) for implementation.

**Structure:**

- **Entities:** Objects (e.g., Student, Course).
- **Attributes:** Properties (e.g., StudentName, CourseCode).
- **Relationships:** Associations between entities (e.g., Student enrolls in Course).

**Example:** In a library: Entity **Book** (attributes: ISBN, Title, Author); Entity **Member** (MemberID, Name); Relationship **Borrows** (Member borrows Book, with attribute DueDate).

ER is not a storage model; it is a design tool. The relational model is where the data actually lives in most systems.

## 4.5 Object-Oriented Data Model

**Concept:** Combines database capabilities with object-oriented concepts: objects, classes, encapsulation, inheritance, and object identity. Complex structures and behavior can be stored and invoked in the database.

**Structure:** Classes define structure and methods; objects are instances. Relationships can be references (object IDs) or embedded objects. Support for inheritance hierarchies.

**Example:** Class **Employee** with attributes (name, salary) and methods (computeBonus()). Subclass **Manager** inherits from Employee and adds (department, subordinates). An object can reference another object (e.g., manager).

**Use:** Object-oriented DBMS (OODBMS) and object-relational extensions (e.g., PostgreSQL with types and functions) support this. Often used when application and data structures are strongly object-oriented.

## 4.6 Comparison Between Models

| Model | Structure | Relationships | Query style | Typical use |
|-------|-----------|---------------|-------------|-------------|
| Hierarchical | Tree | One parent per child | Navigational | Legacy (e.g., IMS) |
| Network | Graph | Many-to-many via sets | Navigational | Legacy (e.g., IDMS) |
| Relational | Tables | Via keys and joins | Declarative (SQL) | Most modern applications |
| ER | Entities, attributes, relationships | Explicit in diagram | Design only | Conceptual design |
| Object-Oriented | Classes, objects | References, inheritance | OO APIs / OQL | Complex domains, OO apps |

---

# 5. Keys in DBMS

Keys are attributes (or sets of attributes) used to identify rows and enforce uniqueness and relationships.

## 5.1 Super Key

**Definition:** A **super key** is a set of one or more attributes that uniquely identifies every row in a relation.

- Uniqueness is required; minimality is not. So a super key can have extra attributes.
- Every table has at least one super key (the set of all attributes is always a super key).

**Example:** For a table **Student(StudentID, Name, Email, DOB)**:
- {StudentID} is a super key.
- {StudentID, Name} is also a super key.
- {Email} is a super key if Email is unique.
- {Name, DOB} is a super key only if the combination is unique.

## 5.2 Candidate Key

**Definition:** A **candidate key** is a minimal super key: it uniquely identifies every row, and no proper subset of it does.

- So a candidate key is a super key with no redundant attributes.
- A relation can have more than one candidate key.

**Example:** In **Student(StudentID, Name, Email, DOB)**:
- If StudentID and Email are both unique, {StudentID} and {Email} are candidate keys.
- {StudentID, Name} is not a candidate key because {StudentID} alone is already a key (Name is redundant).

## 5.3 Primary Key

**Definition:** The **primary key** is the candidate key chosen by the designer to be the main identifier of the relation. It must be unique and not null for every row.

- One primary key per table.
- Used for indexing and as the main reference target for foreign keys.

**Example:** In **Student(StudentID, Name, Email)**, if we choose StudentID as primary key, then StudentID must be unique and not null for every student.

## 5.4 Alternate Key

**Definition:** **Alternate keys** are candidate keys that are not selected as the primary key.

**Example:** In **Student(StudentID, Name, Email)** with StudentID as primary key, Email can be an alternate key if it is unique. Applications can still enforce uniqueness on Email (e.g., unique constraint).

## 5.5 Foreign Key

**Definition:** A **foreign key** is an attribute (or set of attributes) in one relation that refers to the primary key (or a unique key) of another relation. It enforces referential integrity: the referenced row must exist (or the foreign key value can be null if allowed).

**Example:**

- **Orders(OrderID, CustomerID, OrderDate)**  
- **Customers(CustomerID, Name, Address)**  

CustomerID in Orders is a foreign key referencing Customers(CustomerID). Every non-null CustomerID in Orders must exist in Customers.

## 5.6 Composite Key

**Definition:** A **composite key** is a key that consists of two or more attributes. It can be a primary key, candidate key, or part of a foreign key.

**Example:** **Enrollments(StudentID, CourseID, Grade)**. A student can enroll in many courses and a course has many students. (StudentID, CourseID) together uniquely identify an enrollment, so (StudentID, CourseID) is a composite primary key.

## 5.7 Surrogate Key

**Definition:** A **surrogate key** is an artificial key (often auto-generated, e.g., auto-increment integer or UUID) that has no business meaning. It is used solely as a unique, stable identifier.

**Example:** **Customers(CustomerID, NationalID, Name)**. NationalID might be the "natural" identifier, but it can change (e.g., country rules) or be sensitive. CustomerID could be a surrogate (e.g., 1, 2, 3, ...) used internally and in foreign keys; NationalID remains for business use and validation.

**Advantages:** Stable (does not change), simple (single column), and avoids exposing or depending on business identifiers in relationships.

---

# 6. Advantages and Disadvantages of DBMS

## 6.1 Advantages

| Advantage | Description |
|-----------|-------------|
| **Data sharing** | Multiple users and applications can access the same data in a controlled way. |
| **Reduced redundancy** | Data stored once; normalization and design minimize duplication. |
| **Data consistency** | Single source of truth and constraints keep data aligned. |
| **Data integrity** | Rules (keys, checks, triggers) enforce correctness. |
| **Security** | Authentication, authorization, encryption, and auditing. |
| **Backup and recovery** | Built-in backup, logging, and recovery mechanisms. |
| **Concurrent access** | Multiple users without corrupting data (transactions, locking). |
| **Data independence** | Physical storage can change without changing application logic (to a degree). |
| **Standardized access** | SQL and APIs provide a uniform way to query and update data. |
| **Centralized management** | DBA can tune, secure, and maintain data in one place. |

## 6.2 Disadvantages

| Disadvantage | Description |
|--------------|-------------|
| **Complexity** | DBMS software and design are complex; training and expertise needed. |
| **Cost** | Licensing (for commercial DBMS), hardware, and DBA resources. |
| **Performance overhead** | Abstraction, concurrency control, and integrity checks add overhead compared to raw files. |
| **Single point of failure** | If the DBMS or server fails, many applications are affected (mitigated by clustering, replication). |
| **Scalability limits** | Single-server DBMS can hit limits; scaling may require distribution, sharding, or different systems. |
| **Migration** | Moving to another DBMS or version can be costly and risky. |
| **Overkill for simple needs** | For tiny, single-user, read-only data, a simple file might suffice. |

---

# 7. Entity Relationship Model (ER Model)

The ER model is a conceptual tool for designing databases by modeling the real world as entities, attributes, and relationships.

## 7.1 Entities

**Definition:** An **entity** is a real-world object or concept that can be distinctly identified and about which we store information.

- **Entity type:** A category (e.g., Student, Course, Order).
- **Entity instance:** A specific occurrence (e.g., the student "John", the course "DBMS").

**Example:** In a hospital, entities might be Patient, Doctor, Ward, and Appointment.

## 7.2 Attributes

Attributes are properties that describe an entity.

### 7.2.1 Simple (Atomic) Attribute

Cannot be divided into smaller meaningful parts.

**Example:** Age, Salary, StudentID.

### 7.2.2 Composite Attribute

Can be split into smaller components.

**Example:** Address -> {Street, City, State, Zip}. Name -> {FirstName, LastName}.

### 7.2.3 Derived Attribute

Value can be computed from other attributes or entities.

**Example:** Age derived from DateOfBirth. TotalOrderValue derived from sum of OrderLines.

### 7.2.4 Multivalued Attribute

Can hold multiple values for one entity instance.

**Example:** PhoneNumbers (a person may have home, work, mobile). EmailAddresses. Skills (an employee has many skills).

In the relational model, multivalued attributes are often represented in a separate table (e.g., EmployeeSkills).

## 7.3 Relationships

**Definition:** A **relationship** is an association among two or more entities. It represents how entities are connected in the real world.

**Example:** Student **enrolls in** Course. Employee **works in** Department. Customer **places** Order.

Relationships can have **attributes** (e.g., Enrolls has Grade, DateEnrolled).

## 7.4 Types of Relationships

### 7.4.1 One to One (1:1)

One entity instance of A is associated with at most one instance of B, and vice versa.

**Example:** One Country has one Capital City; one Capital City is capital of one Country.  
**Another:** One Person has one Passport; one Passport belongs to one Person.

### 7.4.2 One to Many (1:N) or Many to One (N:1)

One instance of A can be associated with many instances of B; each B is associated with at most one A.

**Example:** One Department has many Employees; each Employee works in one Department.  
**Another:** One Customer places many Orders; each Order is placed by one Customer.

### 7.4.3 Many to Many (M:N)

One instance of A can be associated with many B's, and one B with many A's.

**Example:** Students enroll in many Courses; Courses have many Students.  
**Another:** Authors write many Books; Books have many Authors.

In the relational model, M:N is implemented via a **junction table** (e.g., Enrollments, BookAuthors).

## 7.5 Weak Entities

**Definition:** A **weak entity** is an entity that cannot be uniquely identified by its own attributes alone; it depends on another entity (the **owner** or **identifying entity**). Its primary key includes the primary key of the owner.

**Example:** In an insurance company, **Dependent** (e.g., spouse, child) might be a weak entity of **Employee**. A dependent is identified by (EmployeeID, DependentName) because the same name could appear for different employees.

**Notation:** Weak entity is often shown in a double rectangle; identifying relationship in a double diamond.

## 7.6 ER Diagrams

ER diagrams use standard symbols:

- **Rectangle:** Entity type.
- **Ellipse:** Attribute (underlined = key attribute).
- **Diamond:** Relationship.
- **Lines:** Connect entities to relationships and attributes to entities. Cardinality (1, N, M) is shown on lines.
- **Double rectangle:** Weak entity.
- **Double diamond:** Identifying relationship.

**Simple example:**

```
    [Employee] -----< works in >---- [Department]
         |                                |
    EmpID, Name                    DeptID, Name
```

## 7.7 Real-World Example

**Library system:**

- **Entities:** Member (MemberID, Name, JoinDate), Book (ISBN, Title, Author), Copy (CopyID, ISBN).
- **Relationships:** Member **Borrows** Copy (with DueDate, ReturnDate); Copy **is copy of** Book.
- **Cardinality:** One Member borrows many Copies (over time); one Copy is borrowed by one Member at a time. One Book has many Copies.

---

# 8. Cardinality and Participation Constraints

## 8.1 Cardinality Ratios

**Cardinality** describes how many instances of one entity can be associated with how many instances of another entity through a relationship.

- **1:1** â€“ One to one.
- **1:N** â€“ One to many.
- **M:N** â€“ Many to many.

**Example:** Department : Employee = 1:N (one department, many employees). Student : Course = M:N (many-to-many); the junction table Enrollments stores the relationship.

## 8.2 Total Participation

**Total participation** (mandatory participation) means every instance of an entity must participate in the relationship. In the diagram, the entity is connected to the relationship with a double line.

**Example:** Every Order must be placed by some Customer. So Order totally participates in "places" with Customer. If an order cannot exist without a customer, Orderâ€™s participation is total.

## 8.3 Partial Participation

**Partial participation** (optional) means not every instance of the entity need participate in the relationship. Shown with a single line.

**Example:** Not every Customer has placed an Order (new customers). So Customer partially participates in "places" with Order.

## 8.4 Examples

- **Employee â€“ Department (works in):** Each Employee works in exactly one Department (total participation of Employee). Each Department has at least one Employee (or not, if departments can be empty) â€“ if we require at least one employee, Department totally participates; otherwise partial.
- **Student â€“ Course (enrolls in):** Student can enroll in zero or more courses (partial); Course can have zero or more students (partial). Cardinality M:N.

---

# 9. Keys in ER Model

## 9.1 Primary Key

In the ER model, one or more attributes of an entity type that uniquely identify an entity instance form the **primary key**. Underlined in the ER diagram.

**Example:** Student: StudentID. Course: CourseID.

## 9.2 Composite Key

When a single attribute is not enough, a **composite key** (multiple attributes together) is used.

**Example:** Enrollments: (StudentID, CourseID). A weak entityâ€™s key is often composite, including the ownerâ€™s key (e.g., Dependent: (EmployeeID, DependentName)).

## 9.3 Weak Entity Keys

A **weak entity** has no key of its own. Its **discriminator** (or partial key) distinguishes instances within the same owner. Full key = ownerâ€™s primary key + discriminator.

**Example:** Dependent(EmployeeID, DependentName). EmployeeID comes from Employee; DependentName is the discriminator. (EmployeeID, DependentName) is the full key.

---

# 10. Relational Data Model

The relational model represents data as relations (tables) and uses keys and constraints to enforce structure and integrity.

## 10.1 Tables (Relations)

A **relation** is a table with a name. It has a fixed set of columns (attributes); each column has a name and a domain (type). The order of columns is fixed; the order of rows is not significant.

**Example:** **Employee(EmpID, Name, DeptID, Salary)**.

## 10.2 Rows (Tuples)

A **row** (tuple) is one horizontal record in the table. Each row has one value per attribute. A row represents one entity instance.

**Example:** (101, 'Alice', 5, 60000) is one row in Employee.

## 10.3 Columns (Attributes)

A **column** (attribute) is a named, typed vertical component. All values in a column come from the same domain.

**Example:** Name is a column; all values are strings representing employee names.

## 10.4 Schema vs Instance

- **Schema:** The structure of the database: relation names, attribute names, types, and constraints (keys, checks, etc.). It changes rarely.
- **Instance:** The actual data (set of rows) in the database at a point in time. It changes with every insert, update, delete.

**Example:** Schema: Employee(EmpID INT, Name VARCHAR(100), DeptID INT, Salary DECIMAL(10,2)), PRIMARY KEY (EmpID). Instance: the current set of rows in Employee.

## 10.5 Constraints

| Constraint | Purpose |
|------------|---------|
| **Primary key** | Uniqueness and non-null for identifying rows. |
| **Foreign key** | Referential integrity: referenced row must exist (or FK is null). |
| **Unique** | No duplicate values in the column(s) (nulls may be allowed once). |
| **Not null** | Attribute cannot be null. |
| **Check** | Values must satisfy a condition (e.g., Salary > 0, Age BETWEEN 0 AND 150). |
| **Default** | Value when none is supplied on insert. |

These constraints keep the instance consistent with the intended schema and business rules.

---

# 11. SQL Basics

SQL (Structured Query Language) is used to define, manipulate, and query data in relational databases. Below are basic constructs with simple examples. Assume a table **Employees(EmpID, Name, DeptID, Salary)** and **Departments(DeptID, DeptName)**.

## 11.1 CREATE

Defines a new table (or other object).

```sql
CREATE TABLE Employees (
    EmpID      INT PRIMARY KEY,
    Name       VARCHAR(100) NOT NULL,
    DeptID     INT,
    Salary     DECIMAL(10,2) CHECK (Salary >= 0),
    FOREIGN KEY (DeptID) REFERENCES Departments(DeptID)
);
```

## 11.2 INSERT

Adds rows to a table.

```sql
INSERT INTO Employees (EmpID, Name, DeptID, Salary)
VALUES (1, 'Alice', 10, 55000);

INSERT INTO Employees VALUES (2, 'Bob', 10, 60000);
```

## 11.3 SELECT

Retrieves data. * means all columns.

```sql
SELECT * FROM Employees;

SELECT Name, Salary FROM Employees;

SELECT DISTINCT DeptID FROM Employees;
```

## 11.4 UPDATE

Modifies existing rows.

```sql
UPDATE Employees
SET Salary = 58000
WHERE EmpID = 1;

UPDATE Employees
SET Salary = Salary * 1.1
WHERE DeptID = 10;
```

## 11.5 DELETE

Removes rows.

```sql
DELETE FROM Employees WHERE EmpID = 2;

DELETE FROM Employees WHERE Salary < 30000;
```

## 11.6 WHERE

Filters rows by condition.

```sql
SELECT * FROM Employees WHERE DeptID = 10;

SELECT Name FROM Employees WHERE Salary > 50000 AND DeptID = 10;
```

## 11.7 JOIN

Combines rows from two (or more) tables based on a condition. **INNER JOIN** returns only matching rows; **LEFT JOIN** returns all from left and matches from right (nulls where no match).

```sql
-- Inner join: employees with department names
SELECT E.Name, D.DeptName, E.Salary
FROM Employees E
INNER JOIN Departments D ON E.DeptID = D.DeptID;

-- Left join: all employees, department name if exists
SELECT E.Name, D.DeptName
FROM Employees E
LEFT JOIN Departments D ON E.DeptID = D.DeptID;
```

## 11.8 GROUP BY

Groups rows by one or more columns; aggregate functions (COUNT, SUM, AVG, etc.) apply per group.

```sql
SELECT DeptID, COUNT(*) AS NumEmployees, AVG(Salary) AS AvgSalary
FROM Employees
GROUP BY DeptID;
```

## 11.9 HAVING

Filters groups (after aggregation). WHERE filters rows before grouping; HAVING filters groups.

```sql
SELECT DeptID, AVG(Salary) AS AvgSalary
FROM Employees
GROUP BY DeptID
HAVING AVG(Salary) > 50000;
```

## 11.10 ORDER BY

Sorts the result.

```sql
SELECT Name, Salary FROM Employees ORDER BY Salary DESC;

SELECT Name, DeptID, Salary
FROM Employees
ORDER BY DeptID, Salary DESC;
```

---

# 12. Normalization (Explain in Detail)

Normalization is a design process that organizes data into tables to minimize redundancy and update anomalies while preserving correctness.

## 12.1 Why Normalization is Needed

**Problems without normalization:**

- **Redundancy:** Same fact stored in multiple places (e.g., customer name in every order row).
- **Update anomaly:** Changing the fact in one place but not others (e.g., customer name updated in one order, not in others).
- **Insertion anomaly:** Cannot insert a fact without another (e.g., cannot add a new product category without having a product).
- **Deletion anomaly:** Deleting one fact accidentally removes another (e.g., deleting the last order for a customer removes the customerâ€™s address).

Normalization splits tables so each fact is stored once and dependencies between attributes are logical.

## 12.2 Functional Dependency

**Definition:** In a relation R, a set of attributes X **functionally determines** Y (written X -> Y) if, for every valid instance of R, whenever two rows agree on X they also agree on Y. So X uniquely determines Y.

**Example:** In **Student(StudentID, Name, DeptID, DeptName)**:
- StudentID -> Name, DeptID, DeptName (StudentID determines everything).
- DeptID -> DeptName (department determines its name).

**Full functional dependency:** X -> Y is full if no proper subset of X determines Y.  
**Partial dependency:** Y depends on only part of a candidate key (relevant for 2NF).  
**Transitive dependency:** X -> Y and Y -> Z, and Y is not a key (relevant for 3NF).

## 12.3 Types of Dependencies

- **Trivial:** Y is subset of X (X -> Y always holds).
- **Non-trivial:** Y is not a subset of X.
- **Partial:** For a key K, some attribute A depends on only part of K (e.g., K = (A,B), and A -> C).
- **Transitive:** A -> B and B -> C where B is not a (candidate) key.

## 12.4 First Normal Form (1NF)

**Definition:** Every attribute contains only atomic (indivisible) values, and each row is unique (e.g., has a primary key). No repeating groups.

**Problem it solves:** Repeating groups and multi-valued attributes in one cell (e.g., storing multiple phone numbers in one column or one row per order with columns Phone1, Phone2, Phone3).

**Before (not 1NF):** One row per order with "Items" as a list: OrderID, CustomerID, Items (e.g., "Apple, Banana, Orange").

**After (1NF):** Either one row per order-item (OrderID, CustomerID, Item) or separate Orders and OrderItems tables with one item per row. Each cell has a single value.

**Example:**

| EmpID | Name  | Skills (not 1NF) |
|-------|-------|------------------|
| 1     | Alice | Java, SQL, C#    |

**1NF:** Move skills to separate rows or a separate table:

| EmpID | Name  | Skill |
|-------|-------|-------|
| 1     | Alice | Java  |
| 1     | Alice | SQL   |
| 1     | Alice | C#    |

## 12.5 Second Normal Form (2NF)

**Definition:** Relation is in 1NF and every non-key attribute is fully dependent on the **entire** primary key (no partial dependency).

**Problem it solves:** Partial dependencies cause redundancy. If the key is composite (e.g., OrderID, ProductID) and one non-key attribute depends only on OrderID, that attribute is repeated for every product in the order.

**Example:** **OrderDetails(OrderID, ProductID, ProductName, Quantity)**. Key: (OrderID, ProductID). ProductName depends only on ProductID (partial dependency). So ProductName is repeated for every line of the same product.

**After 2NF:** Split into **OrderDetails(OrderID, ProductID, Quantity)** and **Products(ProductID, ProductName)**. ProductName stored once per product.

## 12.6 Third Normal Form (3NF)

**Definition:** Relation is in 2NF and no non-key attribute depends on another non-key attribute (no transitive dependency).

**Problem it solves:** Transitive dependencies cause redundancy and update anomalies. If A -> B and B -> C, then B and C are repeated for every row with the same B.

**Example:** **Employee(EmpID, Name, DeptID, DeptName)**. EmpID -> DeptID and DeptID -> DeptName. DeptName is transitively dependent on EmpID via DeptID. DeptName is repeated for every employee in the same department.

**After 3NF:** **Employee(EmpID, Name, DeptID)** and **Department(DeptID, DeptName)**. DeptName stored once per department.

## 12.7 Boyce-Codd Normal Form (BCNF)

**Definition:** For every non-trivial functional dependency X -> Y, X is a super key. So every determinant (left side of an FD) is a candidate key.

**Problem it solves:** 3NF allows a non-key attribute to determine another non-key attribute in special cases (when the determinant is not a key). BCNF removes that.

**Example:** **R(Student, Course, Instructor)** with: each instructor teaches one course; each course has many instructors; a student takes a course with one instructor. FDs: (Student, Course) -> Instructor; Instructor -> Course. So (Student, Instructor) -> Course. Candidate keys: (Student, Course) and (Student, Instructor). Instructor -> Course has a non-key determinant (Instructor), so R is not in BCNF.

**After BCNF:** Split: **Enrollment(Student, Instructor)** and **Teaches(Instructor, Course)**. Now every FDâ€™s left side is a key.

## 12.8 Fourth Normal Form (4NF)

**Definition:** Relation is in BCNF and has no non-trivial **multivalued dependency** (MVD) unless the left side is a super key. For X ->> Y (X multidetermines Y), X must be a super key.

**Problem it solves:** Independent multivalued facts in one table cause repetition. If X ->> Y and X ->> Z and Y, Z are independent, we get combinatorial explosion of rows.

**Example:** **Employee(EmpID, Skill, Project)** with MVDs EmpID ->> Skill and EmpID ->> Project (skills and projects independent). One employee with 2 skills and 2 projects gives 4 rows. Same skill and project repeated.

**After 4NF:** **EmpSkills(EmpID, Skill)** and **EmpProjects(EmpID, Project)**. Each fact stored once.

## 12.9 Fifth Normal Form (5NF)

**Definition:** Relation is in 4NF and has no non-trivial **join dependency** that is not implied by the candidate keys. Every join dependency is implied by the keys.

**Problem it solves:** Tables that can only be decomposed and then recombined by a specific join (lossless) without losing information. 5NF ensures the relation cannot be decomposed further without losing information or introducing constraints that are not implied by keys.

**Concept:** Less common in practice. Used when relationships are genuinely three-way or n-way and the only way to avoid redundancy is to split and rejoin. Often skipped in introductory courses.

---

# 13. Query Processing and Optimization

## 13.1 Query Execution

When a query is submitted, the DBMS:

1. **Parses** the query (syntax check, build parse tree).
2. **Translates** into an internal form (e.g., relational algebra).
3. **Optimizes** by choosing an execution plan (order of operations, algorithms, use of indexes).
4. **Executes** the plan and returns the result.

## 13.2 Query Plans

A **query plan** (execution plan) is a tree of operators (e.g., Scan, Index Seek, Join, Sort, Aggregate). Each operator produces a result that feeds the next. The DBMS can show the plan (e.g., EXPLAIN in MySQL/PostgreSQL) so DBAs can see how a query will run.

**Example:** SELECT * FROM Orders WHERE CustomerID = 5 might use: Index Seek on CustomerID -> return rows. Without an index: Full Table Scan on Orders, Filter CustomerID = 5.

## 13.3 Cost-Based Optimization

The optimizer estimates the **cost** of different plans (often in I/O and CPU) and chooses a low-cost plan. It uses statistics (row counts, value distributions, index presence) to estimate cardinality and cost. Cost-based optimization is why creating indexes and updating statistics matter.

## 13.4 Index Usage

**Indexes** are structures (e.g., B-trees) that allow fast lookup by key value. They speed up WHERE, JOIN, and ORDER BY but add overhead for INSERT/UPDATE/DELETE and storage.

- **Clustered index:** Table rows are stored in index order (usually one per table).
- **Non-clustered index:** Separate structure pointing to rows; good for filtering/sorting on non-clustered columns.

Choosing indexes on filtered/joined/sorted columns often greatly improves query performance.

## 13.5 Optimization Techniques

- **Choosing join order:** Join smaller or more selective relations first to reduce intermediate size.
- **Using indexes:** Prefer index seek over full scan when selective.
- **Pushing down predicates:** Apply WHERE as early as possible to reduce rows before joins.
- **Avoiding unnecessary columns:** Select only needed columns to reduce I/O.
- **Caching/buffers:** Keep hot data in memory to reduce disk I/O.

---

# 14. Transactions and Concurrency Control

## 14.1 Transaction Concept

A **transaction** is a logical unit of work: a sequence of one or more operations (reads/writes) that either all succeed (commit) or all are undone (rollback). The database moves from one consistent state to another.

**Example:** Transfer $100 from Account A to B: debit A, credit B. Both must happen or neither; otherwise money is lost or created.

## 14.2 ACID Properties

| Property | Meaning |
|----------|---------|
| **Atomicity** | All operations in the transaction are done as one unit; either all take effect or none do. |
| **Consistency** | The transaction leaves the database in a state that satisfies all defined rules (constraints, invariants). |
| **Isolation** | Concurrent transactions do not see each otherâ€™s uncommitted changes; they appear to run one after another. |
| **Durability** | Once a transaction commits, its effects persist even after system failure. |

## 14.3 Transaction States

- **Active:** Operations are being executed.
- **Partially committed:** Last operation done; before commit is finalized (e.g., before log flush).
- **Committed:** Transaction completed successfully; effects are permanent.
- **Failed:** Transaction cannot proceed (e.g., error, deadlock).
- **Aborted:** Transaction rolled back; database restored to state before the transaction.
- **Terminated:** Either committed or aborted; no further state changes.

## 14.4 Schedules

A **schedule** is an ordering of operations from one or more transactions. It defines the interleaving of reads and writes.

### 14.4.1 Serial Schedules

Operations of one transaction run consecutively without any operation from another transaction in between. One transaction fully completes before the next starts. Serial schedules are always correct (assuming each transaction alone is correct) but limit concurrency.

### 14.4.2 Non-Serial Schedules

Operations of different transactions are interleaved. Better concurrency but may produce wrong results (e.g., lost update, dirty read) if not controlled.

### 14.4.3 Serializable Schedules

A schedule is **serializable** if its effect (final database state and outputs) is equivalent to some serial schedule of the same transactions. So it is â€œcorrectâ€ even though operations were interleaved. **Conflict serializability** and **view serializability** are two definitions used in theory; conflict serializability is what most systems enforce (e.g., via two-phase locking or conflict detection).

---

# 15. Concurrency Control Techniques

## 15.1 Lock-Based Protocols

**Lock:** A mechanism that gives a transaction exclusive or shared access to a data item.

- **Shared lock (S):** Multiple transactions can hold S on the same item for reading; no exclusive lock.
- **Exclusive lock (X):** Only one transaction can hold X; used for writes. No other S or X allowed.

Rules: Before read, acquire S (or X). Before write, acquire X. Release locks according to the protocol (e.g., two-phase locking) to ensure serializability.

## 15.2 Two-Phase Locking (2PL)

**Rule:** In every transaction, all lock acquisitions happen in one phase and all lock releases in a later phase. So once a transaction releases any lock, it cannot acquire new locks.

- **Growing phase:** Transaction acquires locks.
- **Shrinking phase:** Transaction releases locks.

2PL guarantees conflict serializability. **Strict 2PL** holds all locks until commit/abort, which avoids cascading aborts and simplifies recovery.

## 15.3 Timestamp Ordering

Each transaction gets a unique **timestamp** when it starts. Each data item stores read and write timestamps. Operations are allowed only if they do not violate the order of timestamps (e.g., do not read a value written by a later transaction, or write a value that an older transaction has already read). Transactions that would violate the order are aborted and restarted. No locks, but more aborts and careful handling of timestamps.

## 15.4 Multiversion Concurrency Control (MVCC)

Multiple **versions** of a data item are kept. A read sees a consistent snapshot (e.g., the version visible at the transactionâ€™s start or at a chosen point). Writes create new versions. So readers do not block writers and writers do not block readers in the same way as in lock-based schemes. Used by PostgreSQL, MySQL (InnoDB), and others to provide high concurrency and snapshot isolation.

---

# 16. Deadlocks

## 16.1 What is Deadlock

**Deadlock** is a situation where two or more transactions are waiting for each other to release resources (e.g., locks), so none can proceed.

**Example:** T1 holds lock on A and waits for B; T2 holds lock on B and waits for A. Neither can advance.

## 16.2 Causes

- **Circular wait:** T1 waits for T2, T2 waits for T3, ..., Tn waits for T1.
- **Resource contention:** Transactions need the same set of resources in different orders.
- **Lock ordering:** If transactions do not acquire locks in a consistent global order, cycles can form.

## 16.3 Deadlock Detection

The system maintains a **wait-for graph**: nodes are transactions; edge T1 -> T2 if T1 is waiting for a resource held by T2. Periodically (or on wait), check for a cycle. If a cycle exists, there is a deadlock. One or more transactions in the cycle are chosen as **victims** and aborted to break the deadlock.

## 16.4 Deadlock Prevention

Policies that ensure no deadlock can occur:

- **Lock ordering:** All transactions acquire locks in a fixed global order (e.g., always lock A before B). No cycle can form.
- **Wait-die / Wound-wait:** Use transaction timestamps so that an older transaction never waits for a younger one (or vice versa), preventing cycles.

## 16.5 Deadlock Avoidance

**Avoidance** (e.g., in operating systems with resource allocation graphs) uses advance knowledge of resource needs to grant resources only when it is safe (no future deadlock). In databases, prevention (ordering, timestamp rules) is more common than dynamic avoidance.

---

# 17. Crash Recovery and Backup

## 17.1 Failure Types

| Failure | Description | Example |
|---------|-------------|---------|
| **Transaction failure** | A single transaction aborts (error, deadlock, constraint). | Rollback that transaction. |
| **System failure** | Crash, power loss; main memory lost; disk assumed intact. | Restore consistent state using log and possibly checkpoints. |
| **Media failure** | Disk or storage corruption or loss. | Restore from backup and apply log (if available). |

## 17.2 Recovery Techniques

### 17.2.1 Log-Based Recovery

A **log** is a sequential record of actions: transaction start/commit/abort, and before/after values (or operations) for writes. On recovery:

- **Undo:** For uncommitted transactions, reverse their writes using the log.
- **Redo:** For committed transactions, reapply their writes so that committed effects are on disk.

**Write-ahead logging (WAL):** Before a data page is written to disk, the corresponding log records must be written to stable storage. This ensures we can redo or undo correctly after a crash.

### 17.2.2 Checkpointing

A **checkpoint** is a point at which the system writes certain information to the log (e.g., list of active transactions, dirty pages). During recovery, we only need to consider log records after the last checkpoint and transactions that were active at the checkpoint, which reduces recovery work.

### 17.2.3 Shadow Paging

A transaction works on a copy (shadow) of affected pages. On commit, the shadow becomes the current version; on abort, the shadow is discarded. Simpler conceptually but less used in modern systems compared to log-based recovery.

## 17.3 Recovery Manager and Buffer Manager

- **Recovery manager:** Responsible for logging, checkpointing, and recovery (undo/redo) after a failure.
- **Buffer manager:** Manages pages in main memory (buffer pool), brings pages from disk and writes them back, and coordinates with the recovery manager (e.g., WAL) so that recovery is possible.

## 17.4 Backup and Restore Strategies

- **Full backup:** Copy of the entire database at a point in time. Restore recovers to that point; then apply log (if available) for more recent transactions.
- **Incremental/differential backup:** Only changed data since last full or incremental backup; faster and smaller, but restore may need multiple steps.
- **Log backup:** Backing up transaction logs allows point-in-time recovery (restore full backup + apply logs up to desired time).
- **Strategy:** Regular full backups plus frequent log (or incremental) backups; test restore procedures.

---

# 18. Database Security and Authorization

## 18.1 Authentication

**Authentication** is verifying the identity of a user (or process). The user proves who they are (e.g., password, certificate, SSO). The DBMS often relies on the OS or a central identity provider, or manages its own user/password store.

## 18.2 Authorization

**Authorization** is deciding what an authenticated user is allowed to do: which objects (tables, views) they can access and which operations (SELECT, INSERT, UPDATE, DELETE, EXECUTE) they can perform.

## 18.3 Access Control

**Access control** implements authorization by defining and enforcing rules (e.g., privileges, roles). It can be discretionary (owner grants to others) or mandatory (labels and clearances).

## 18.4 Role-Based Access Control (RBAC)

**Roles** are named sets of privileges (e.g., "Accountant", "Manager"). Users are assigned roles; they get the union of privileges of their roles. This simplifies management: change the role once, all users with that role are updated.

**Example:** Role "Analyst": SELECT on Sales, Reports. Role "DBA": broader admin rights. Assign "Analyst" to many users instead of granting the same set of privileges to each.

## 18.5 Database Privileges

Typical privileges:

- **SELECT:** Read rows.
- **INSERT:** Insert rows.
- **UPDATE:** Update rows (optionally only certain columns).
- **DELETE:** Delete rows.
- **REFERENCES:** Create foreign keys to the table.
- **EXECUTE:** Execute a routine (e.g., stored procedure).

Privileges can be granted on database, schema, table, column, or routine.

## 18.6 SQL GRANT and REVOKE

**GRANT** gives privileges to a user or role. **REVOKE** removes them.

```sql
GRANT SELECT, INSERT ON Orders TO analyst_role;
GRANT ALL ON Employees TO user_john;
REVOKE INSERT ON Orders FROM analyst_role;
```

Some systems support **WITH GRANT OPTION** so the grantee can further grant the same privilege to others.

---

# 19. Distributed Databases

## 19.1 What is a Distributed Database

A **distributed database** is a database that is spread across multiple physical locations (sites/nodes) connected by a network. Data may be partitioned (fragmentation), replicated, or both. To users and applications it can appear as a single logical database.

## 19.2 Advantages

- **Local autonomy:** Sites can manage and query local data locally.
- **Availability:** If one site fails, others can continue (with replicated or non-replicated data).
- **Scalability:** Add more sites or nodes to grow capacity.
- **Performance:** Data can be placed near users to reduce latency and network load.

## 19.3 Challenges

- **Distribution transparency:** Hiding where data lives from users and applications.
- **Transaction management:** Ensuring ACID across sites (distributed transactions, two-phase commit).
- **Consistency:** Keeping replicas consistent (strong vs eventual consistency, replication protocols).
- **Query processing:** Optimizing queries that touch multiple sites (partitioning, joins across network).
- **Heterogeneity:** Different DBMS or schemas at different sites (federated systems).

## 19.4 Key Concepts

### 19.4.1 Fragmentation

Data is split into **fragments** (horizontal: subsets of rows; vertical: subsets of columns; or mixed). Fragments are stored at one or more sites. Fragmentation should preserve reconstructability (union of horizontal fragments, join of vertical fragments) and allow efficient querying.

### 19.4.2 Replication

**Replication** is storing copies of the same data at multiple sites. It improves availability and read performance but complicates updates (keeping all copies consistent) and storage.

### 19.4.3 Allocation

**Allocation** is the decision of where to place each fragment or replica (which site stores what). Goals often include minimizing cost, latency, or maximizing availability.

---

# 20. NoSQL Databases

NoSQL databases are non-relational data stores designed for flexibility, scale, or specific access patterns. They often relax strict ACID or fixed schema.

## 20.1 Key-Value Stores

**Concept:** Data is stored as key-value pairs. Key is unique; value is often an opaque blob. Very fast lookups by key; no query language over value contents (application interprets value).

**Example:** **Redis.** Use cases: caching, session store, simple queues, real-time leaderboards.  
**Example:** **DynamoDB** (with key-value usage).  
**Example:** **Riak.**

## 20.2 Document Databases

**Concept:** Data is stored as **documents** (e.g., JSON/BSON). Each document has a unique ID and can have nested structure. Queries can target fields inside documents. Schema can vary per document (schema-flexible).

**Example:** **MongoDB.** Use cases: content management, catalogs, user profiles, event logs.  
**Example:** **Couchbase.**  
**Example:** **Firestore.**

## 20.3 Column-Family (Wide-Column) Stores

**Concept:** Data is organized by **column families** (groups of columns). Rows are identified by a row key; within a family, columns can be sparse and many. Optimized for reads/writes by row key and column set; good for analytics and high write throughput.

**Example:** **Apache Cassandra.** Use cases: time-series, write-heavy workloads, multi-datacenter replication.  
**Example:** **HBase.**  
**Example:** **ScyllaDB.**

## 20.4 Graph Databases

**Concept:** Data is represented as **nodes** (entities) and **edges** (relationships). Queries traverse the graph (e.g., "friends of friends", path finding, recommendations). Optimized for relationship-heavy data.

**Example:** **Neo4j.** Use cases: social networks, recommendation engines, fraud detection, knowledge graphs.  
**Example:** **Amazon Neptune.**

## 20.5 NoSQL vs Relational Comparison

| Aspect | Relational (SQL) | NoSQL |
|--------|------------------|--------|
| Schema | Fixed schema (tables, columns) | Flexible or schema-less (documents, key-value) |
| Transactions | ACID common | Often eventual consistency; some support ACID |
| Query | Declarative SQL, joins | API- or query-language specific; joins often in app or limited |
| Scale | Vertical + partitioning/replication | Horizontal scaling common (sharding, distribution) |
| Use case | Structured, consistent, complex queries | High throughput, flexible structure, simple key or document access, graph traversal |

---

# 21. Big Data Concepts

## 21.1 What is Big Data

**Big Data** refers to datasets that are too large, fast-changing, or complex to be handled efficiently by traditional database and processing tools. "Big" refers to volume, velocity, variety, and sometimes veracity and value.

## 21.2 3Vs / 5Vs

- **Volume:** Huge amount of data (terabytes to exabytes).
- **Velocity:** Data generated and processed at high speed (streaming, real-time).
- **Variety:** Different types: structured, semi-structured (JSON, XML), unstructured (text, images, video).
- **Veracity** (often in 5Vs): Quality, uncertainty, and reliability of data.
- **Value:** Extracting useful insights and business value from the data.

## 21.3 Challenges

- **Storage:** Cost-effective, scalable storage (distributed file systems, object storage).
- **Processing:** Parallel and distributed processing (e.g., MapReduce, Spark).
- **Analysis:** Querying and analytics at scale; machine learning on large data.
- **Integration:** Combining data from many sources and formats.
- **Privacy and security:** Protecting sensitive data at scale.

Technologies often associated with big data: Hadoop (HDFS, MapReduce), Apache Spark, Kafka (streaming), cloud data lakes, and various NoSQL and analytical databases.

---

# 22. Data Warehousing

## 22.1 What is a Data Warehouse

A **data warehouse** is a central repository of integrated, historical data from one or more sources, designed for querying and analysis (OLAP) rather than day-to-day transaction processing (OLTP). Data is typically subject-oriented, integrated, non-volatile, and time-variant.

- **Subject-oriented:** Organized by subject (e.g., sales, customers), not by application.
- **Integrated:** Data from different sources is cleaned and unified.
- **Non-volatile:** Data is loaded and then read; not updated in place like in OLTP.
- **Time-variant:** History is kept for trend and period analysis.

## 22.2 OLTP vs OLAP

| Aspect | OLTP (On-Line Transaction Processing) | OLAP (On-Line Analytical Processing) |
|--------|----------------------------------------|--------------------------------------|
| Purpose | Daily operations (orders, payments) | Analysis, reporting, decision support |
| Data | Current, detailed | Historical, aggregated, summarized |
| Operations | Many short transactions (insert, update) | Complex reads, scans, aggregations |
| Users | Clerks, applications | Analysts, managers |
| Schema | Normalized (3NF) | Often denormalized (star/snowflake) |
| Example | Order entry, banking transactions | Sales by region and time, trend reports |

## 22.3 Data Warehouse Architecture

Typical components:

- **Sources:** OLTP databases, files, external systems.
- **ETL:** Extract, Transform, Load â€“ moves and prepares data for the warehouse.
- **Staging:** Temporary storage during ETL.
- **Data warehouse:** Central store (often relational) with fact and dimension tables (star/snowflake schemas).
- **Data marts:** Subset of the warehouse for a department or subject (e.g., sales mart).
- **Presentation layer:** Reports, dashboards, OLAP cubes, BI tools.

---

# 23. ETL Process

ETL (Extract, Transform, Load) is the process of moving data from source systems into a target system (e.g., data warehouse or data lake).

## 23.1 Extract

**Extract** is reading data from source systems (databases, files, APIs). It can be full (entire table/file) or incremental (only new or changed data). Challenges: different formats, access methods, and impact on source systems (e.g., avoiding heavy reads during peak).

**Example:** Extract daily sales from the OLTP Orders and OrderDetails tables; extract customer list from CRM API.

## 23.2 Transform

**Transform** is cleaning, converting, and enriching data so it fits the target model and quality standards. Typical steps:

- **Cleaning:** Fix missing values, duplicates, invalid codes.
- **Mapping:** Convert codes (e.g., source "US" -> target "United States"), standardize units and formats.
- **Aggregation:** Summarize (e.g., daily totals by store).
- **Derivation:** Compute new fields (e.g., profit = revenue - cost).
- **Validation:** Enforce rules and reject or flag bad rows.

**Example:** Convert all dates to one time zone; replace product IDs with warehouse product keys; compute total amount and discount per order line.

## 23.3 Load

**Load** is writing the transformed data into the target system (warehouse tables, partitions, or files). Can be full refresh (replace) or incremental (append or merge). Often done in bulk for performance; may run in a maintenance window.

**Example:** Load the dayâ€™s sales facts into the SalesFact table and update the Customer dimension with new or changed customers.

## 23.4 Simple Real-World Example

**Scenario:** A retail company loads daily sales into a data warehouse.

1. **Extract:** Nightly job connects to the storeâ€™s OLTP database and extracts all orders and order lines for the previous day (or only changed records if supported).
2. **Transform:**  
   - Join orders with product and store tables to get product names and store names.  
   - Convert currency to a standard currency.  
   - Compute line total and discount.  
   - Filter out test or cancelled orders.  
   - Map store IDs to warehouse Store dimension keys.
3. **Load:** Insert the resulting rows into the warehouse **SalesFact** table (date, store key, product key, quantity, amount, etc.). Update **Store** and **Product** dimension tables if there are new or changed attributes.

The next morning, analysts can run reports and dashboards on the updated warehouse.

---

# 24. Additional Topics (Indexing and Views)

## 24.1 Indexing (Brief)

**Indexes** are auxiliary structures that speed up search and sort operations at the cost of extra space and write overhead.

- **B-tree index:** Default in most RDBMS; good for range and equality on one or more columns.
- **Hash index:** Good for equality only; no range.
- **Composite index:** Index on (A, B, C); useful for queries that filter/sort on left prefixes (A; A,B; A,B,C).
- **Clustered vs non-clustered:** Clustered defines physical order of table rows; non-clustered is a separate structure with pointers to rows.

Choosing indexes: consider WHERE, JOIN, and ORDER BY columns; avoid too many indexes on heavily updated tables.

## 24.2 Views

A **view** is a named, stored query that behaves like a virtual table. It does not store data (unless materialized); each access runs the underlying query.

**Uses:** Simplify complex queries, restrict columns/rows for security (e.g., hide salary), present a consistent interface when base tables change.

**Example:**

```sql
CREATE VIEW ActiveCustomers AS
SELECT CustomerID, Name, Email
FROM Customers
WHERE Status = 'Active';
```

Users query **ActiveCustomers** instead of writing the full query. **Materialized views** store the result and can be refreshed periodically for faster reads at the cost of freshness and storage.

---

*End of DBMS Theory Notes.*
