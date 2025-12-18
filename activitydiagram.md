# Lost & Found System — Activity Diagram

```mermaid
flowchart TD
    %% Login Process %%
    A(((Start))) --> B[User opens login page]
    B --> C[Enter username and password]
    C --> D{Is input valid?}
    D -- No --> E[Show validation error]
    E --> B
    D -- Yes --> F[Send login request via route.js]
    F --> G[Server AuthService verifies credentials]
    G --> H{Credentials correct?}
    H -- No --> I[Return error response]
    I --> B
    H -- Yes --> J[Create Session object]
    J --> K[Return success and user data]
    K --> L[ui.js renders dashboard based on role]

    %% Dashboard Activities %%
    L --> M{User role}
    M -- Student --> N[View reports and search]
    M -- Student --> AA[Open chat interface] 
    M -- Staff --> O[View and escalate reports]
    M -- Admin --> P[Monitor all reports and chat]

    %% Report Creation %%
    N --> Q[Click Create Report]
    Q --> R[Fill report details]
    R --> S[Submit via main.js to route.js to ReportService]
    S --> T[ReportRepository saves report]
    T --> U[Return confirmation]
    U --> L

    %% Escalation / Admin Actions %%
    O --> V[Select report to escalate]
    V --> W[EscalateReport via ReportService]
    W --> T
    P --> X[Update report status accept or reject]
    X --> T
    P --> Y[Monitor chat messages]
    Y --> Z[MessageService retrieves messages]

    %% Messaging %%
    AA --> AB[Send message via MessageService]
    AB --> AC[MessageRepository saves message]
    AC --> AD{Receiver online?}
    AD -- Yes --> AE[Update receiver DOM via ui.js]
    AD -- No --> AF[Cache message in MessageCache]

    %% XP Handling %%
    T --> AG{Is report found?}
    AG -- Yes --> AH[XPService awardXP to user]
    AH --> AI[Update XP table]
    AG -- No --> AI

    %% Logout / Session End %%
    L --> AJ[User clicks logout]
    AJ --> AK[Destroy session object]
    AK --> AZ(((End)))


    %% Optional loops %%
    AE --> L
    AF --> L
    AI --> L
