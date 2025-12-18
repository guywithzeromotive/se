# **Lost & Found System — Project Overview**

## **1. Packages & File Structure**

```
project-root/
│
├── server/
│   ├── domain/                 # Entities / models
│   │   ├── User.java           «abstract»
│   │   ├── Student.java
│   │   ├── Staff.java
│   │   ├── Admin.java
│   │   ├── Report.java
│   │   ├── Message.java
│   │   ├── XP.java
│   │   └── Session.java
│   │
│   ├── service/
│   │   ├── AuthService.java
│   │   ├── ReportService.java
│   │   ├── MessageService.java
│   │   └── XPService.java
│   │
│   ├── repository/
│   │   ├── UserRepository.java
│   │   ├── ReportRepository.java
│   │   ├── MessageRepository.java
│   │   └── XPRepository.java
│   │
│   ├── util/
│   │   └── CryptoUtil.java
│   │
│   └── initializer/
│       ├── DBInitializer.java
│       └── Validator.java
│
├── client/
│   ├── main.js
│   ├── route.js
│   └── ui.js
│
└── static/
    ├── html/
    ├── css/
    └── images/
```

---

## **2. Database Tables**

| Table                    | Notes                                                    |
| ------------------------ | -------------------------------------------------------- |
| users                    | id, username, passwordHash, name, phone, role            |
| reports                  | id, type, title, description, status, createdAt, ownerId |
| messages                 | id, senderId, receiverId, content, timestamp, status     |
| xp                       | id, userId, academicYear, xpPoints                       |
| sessions                 | sessionId, userId, role, expiresAt                       |
| message_cache (optional) | temporary cache for online messages                      |

---

## **3. Class Diagram**

### **User Hierarchy**

```
+----------------+ «abstract»
|     User       |
+----------------+
| - id          |
| - username    |
| - passwordHash|
| - name        |
| - phone       |
+----------------+
| + getName()   |
| + getPhone()  |
| + verifyPassword(raw) |
+----------------+
           ^
           |
+----------------+   +----------------+   +----------------+
|   Student      |   |    Staff       |   |    Admin       |
+----------------+   +----------------+   +----------------+
```

### **Report**

```
+----------------+
|    Report      |
+----------------+
| - id          |
| - type        |
| - title       |
| - description |
| - status      |
| - createdAt   |
| - ownerId     |
+----------------+
| + getSummary()|
+----------------+
```

### **Message**

```
+----------------+
|   Message      |
+----------------+
| - id          |
| - senderId    |
| - receiverId  |
| - content     |
| - timestamp   |
+----------------+
```

### **Session**

```
+----------------+
|   Session      |
+----------------+
| - sessionId   |
| - userId      |
| - role        |
| - expiresAt   |
+----------------+
```

### **XP**

```
+----------------+
|      XP        |
+----------------+
| - id          |  <<PK>>
| - userId      |  <<FK → User.id>>
| - academicYear|
| - xpPoints    |
+----------------+
| + getXP()     |
| + addXP(points)|
| + resetXP()   |
+----------------+
```

---

## **4. Services**

```
+---------------------+
|   AuthService       |
+---------------------+
| + login(username,password) : Session |
| + logout(sessionId) : void |
| + resetPassword(admin,userId) : String |
+---------------------+

+---------------------+
|   ReportService     |
+---------------------+
| + createReport(user,data) : Report |
| + updateStatus(admin,reportId,status) : void |
| + searchReports(criteria) : List<Report> |
| + escalateReport(staff,reportId) : void |
+---------------------+

+---------------------+
|   MessageService    |
+---------------------+
| + sendMessage(sender,receiver,content) : Message |
| + getMessages(user) : List<Message> |
| + monitorChat(admin) : List<Message> |
+---------------------+

+---------------------+
|   XPService         |
+---------------------+
| + awardXP(user,points) : void |
| + getLeaderboard() : List<User> |
| + getUserXP(user) : int |
+---------------------+
```

---

## **5. Repositories**

```
+---------------------+
|   UserRepository    |
+---------------------+
| + findById(id)      |
| + findByUsername(username) |
| + save(user)        |
+---------------------+

+---------------------+
|   ReportRepository  |
+---------------------+
| + findById(id)      |
| + findByCriteria(criteria) |
| + save(report)      |
+---------------------+

+---------------------+
|   MessageRepository |
+---------------------+
| + findByUser(user)  |
| + save(message)     |
+---------------------+

+---------------------+
|   XPRepository      |
+---------------------+
| + findByUser(user)  |
| + save(xp)          |
+---------------------+
```

---

## **6. Utility / Helper Classes**

```
+---------------------+
|   CryptoUtil        |
+---------------------+
| + hash(password)    |
| + verify(password, hash) : boolean |
+---------------------+

+---------------------+
|   Validator         |
+---------------------+
| + validateUser(user) |
| + validateReport(report) |
+---------------------+

+---------------------+
|   DBInitializer     |
+---------------------+
| + checkTables()      |
| + createMissingTables() |
+---------------------+

+---------------------+
|   PageHandler       |
+---------------------+
| + servePage(pageName) |
+---------------------+

+---------------------+
|   MessageCache      |
+---------------------+
| + addMessage(message) |
| + getMessages(user)  |
| + removeMessage(message) |
+---------------------+
```

---

## **7. Relationships / Multiplicities**

```
User <|-- Student
User <|-- Staff
User <|-- Admin

User 1 ── * Report
User 1 ── * Message (sent)
User 1 ── * Message (received)
User 1 ── * XP
User 1 ── 1 Session
XP 1 ── * XPHistory (optional)
```

---

## **8. Client-side Scripts**

```
client/
├── main.js      # Event listeners, input capture
├── route.js     # Server API requests, fetch JSON
└── ui.js        # DOM rendering and updates
```

* **All UI logic / DOM lives in ui.js**
* **route.js** handles async server communication
* **main.js** orchestrates input & event handling

---

## **9. Enums**

```
UserRole: STUDENT, STAFF, ADMIN
ReportType: LOST, FOUND
ReportStatus: PENDING, ACCEPTED, REJECTED, ESCALATED
```

---

## **10. Optional / Advanced**

* `XPHistory` table/class → track points per report/action
* `ArchivedReports` → old reports for performance
* `MessageCache` → for offline/online message queuing

---

**Counts / Stats:**

| Category        | Count |
| --------------- | ----- |
| Packages (Java) | 5     |
| Classes (Java)  | 25+   |
| JS scripts      | 3     |
| DB tables       | 5–7   |
| Enums           | 3     |