# Lost & Found System — Class Diagram

```mermaid
classDiagram
    %% User Hierarchy %%
    class User {
        <<abstract>>
        - id: String
        - username: String
        - passwordHash: String
        - name: String
        - phone: String
        + getName(): String
        + getPhone(): String
        + verifyPassword(raw: String): boolean
    }

    class Student
    class Staff
    class Admin

    User <|-- Student
    User <|-- Staff
    User <|-- Admin

    %% Report %%
    class Report {
        - id: String
        - type: String
        - title: String
        - description: String
        - status: String
        - createdAt: DateTime
        - ownerId: String
        + getSummary(): String
    }

    %% Message %%
    class Message {
        - id: String
        - senderId: String
        - receiverId: String
        - content: String
        - timestamp: DateTime
    }

    %% Session %%
    class Session {
        - sessionId: String
        - userId: String
        - role: String
        - expiresAt: DateTime
    }

    %% XP %%
    class XP {
        - id: String
        - userId: String
        - academicYear: int
        - xpPoints: int
        + getXP(): int
        + addXP(points: int): void
        + resetXP(): void
    }

    %% Services %%
    class AuthService {
        + login(username, password): Session
        + logout(sessionId): void
        + resetPassword(admin, userId): String
    }

    class ReportService {
        + createReport(user, data): Report
        + updateStatus(admin, reportId, status): void
        + searchReports(criteria): List~Report~
        + escalateReport(staff, reportId): void
    }

    class MessageService {
        + sendMessage(sender, receiver, content): Message
        + getMessages(user): List~Message~
        + monitorChat(admin): List~Message~
    }

    class XPService {
        + awardXP(user, points): void
        + getLeaderboard(): List~User~
        + getUserXP(user): int
    }

    %% Repositories %%
    class UserRepository {
        + findById(id): User
        + findByUsername(username): User
        + save(user): void
    }

    class ReportRepository {
        + findById(id): Report
        + findByCriteria(criteria): List~Report~
        + save(report): void
    }

    class MessageRepository {
        + findByUser(user): List~Message~
        + save(message): void
    }

    class XPRepository {
        + findByUser(user): List~XP~
        + save(xp): void
    }

    %% Utilities %%
    class CryptoUtil {
        + hash(password): String
        + verify(password, hash): boolean
    }

    class Validator {
        + validateUser(user)
        + validateReport(report)
    }

    class DBInitializer {
        + checkTables()
        + createMissingTables()
    }

    class PageHandler {
        + servePage(pageName)
    }

    class MessageCache {
        + addMessage(message)
        + getMessages(user)
        + removeMessage(message)
    }

    %% Relationships %%
    User "1" -- "*" Report : owns
    User "1" -- "*" Message : sent
    User "1" -- "*" Message : received
    User "1" -- "*" XP : has
    User "1" -- "1" Session : active
