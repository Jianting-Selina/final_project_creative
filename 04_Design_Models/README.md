
# Some Design Models

## 11.2 Entity Relationship Diagram (ERD)

``` mermaid
erDiagram
    %% CORE IDENTITY %%
    User {
        UUID UserID PK
        String Email
        String PasswordHash
        Boolean IsActive
    }
    
    Volunteer {
        UUID VolunteerID PK, FK
        Text Bio
        Float TotalVerifiedHours
        Enum BgCheckStatus
    }
    
    OrgMember {
        UUID MemberID PK
        UUID UserID FK
        UUID OrgID FK
        Enum Role
    }

    %% ORGANIZATION & EVENTS %%
    Organization {
        UUID OrgID PK
        String Name
        String RegNumber
        Enum VerificationStatus
    }

    Event {
        UUID EventID PK
        UUID OrgID FK
        String Title
        Point GeoLocation
        Int GeofenceRadius
        JSON Settings
    }

    %% SKILLS & TAXONOMY %%
    Skill {
        Int SkillID PK
        String Name
    }

    EventSkill {
        UUID EventID PK, FK
        Int SkillID PK, FK
        Boolean IsMandatory
    }

    VolunteerSkill {
        UUID VolunteerID PK, FK
        Int SkillID PK, FK
        Enum Proficiency
    }

    %% OPERATIONS (THE LOOP) %%
    Shift {
        UUID ShiftID PK
        UUID EventID FK
        Int Capacity
        Int FilledCount
        DateTime StartTime
        DateTime EndTime
    }

    ShiftApplication {
        UUID AppID PK
        UUID ShiftID FK
        UUID VolunteerID FK
        Enum Status
    }

    TimeLog {
        UUID LogID PK
        UUID ShiftID FK
        UUID VolunteerID FK
        DateTime CheckInTime
        Point CheckInGeo
        Enum AnomalyFlag
        UUID VerifiedByMemberID FK
    }

    Certificate {
        UUID CertID PK
        UUID VolunteerID FK
        UUID OrgID FK
        DateTime GeneratedDate
        String UniqueHash
        String PdfUrl
    }

    %% RELATIONSHIPS %%
    
    %% User Relationships (Composition)
    User ||--o| Volunteer : "has profile (1:0..1)"
    User ||--o{ OrgMember : "can have roles (1:N)"
    
    %% Organization Relationships
    Organization ||--o{ OrgMember : "employs (1:N)"
    Organization ||--o{ Event : "hosts (1:N)"
    Organization ||--o{ Certificate : "issues (1:N)"

    %% Event Relationships
    Event ||--o{ Shift : "defines time slots (1:N)"
    Event ||--o{ EventSkill : "requires (1:N)"
    
    %% Volunteer Relationships
    Volunteer ||--o{ VolunteerSkill : "possesses (1:N)"
    Volunteer ||--o{ ShiftApplication : "applies for (1:N)"
    Volunteer ||--o{ TimeLog : "records work (1:N)"
    Volunteer ||--o{ Certificate : "earns (1:N)"

    %% Skill Matching (Many-to-Many)
    Skill ||--o{ EventSkill : "categorizes (1:N)"
    Skill ||--o{ VolunteerSkill : "categorizes (1:N)"

    %% Operational Flow
    Shift ||--o{ ShiftApplication : "receives (1:N)"
    Shift ||--o{ TimeLog : "generates (1:N)"
    OrgMember ||--o{ TimeLog : "verifies (1:N)"
```

## 13. Class Diagram Class Diagram (CD)
``` mermaid
classDiagram
    %% =======================
    %% 1. CORE IDENTITY & ROLES
    %% =======================
    class User {
        +UUID userID
        -String email
        -String passwordHash
        +Boolean isActive
        +login(pwd: String) Boolean
        +changePassword(old: String, new: String)
    }

    class Volunteer {
        +UUID volunteerID
        +UUID userID
        -Text bio
        -Float totalVerifiedHours
        -Enum bgCheckStatus
        +updateProfile(data: Map)
        +addSkill(skill: Skill, level: Proficiency)
        +applyForShift(shift: Shift) ShiftApplication
        +getCertificateHistory() List~Certificate~
    }

    class OrgMember {
        +UUID memberID
        +UUID userID
        +UUID orgID
        +Enum role
        +verifyLog(log: TimeLog, decision: Boolean)
        +manageEvent(event: Event)
    }

    %% =======================
    %% 2. UTILITIES (Value Objects)
    %% =======================
    class GeoPoint {
        <<Value Object>>
        -Double latitude
        -Double longitude
        -Float accuracyMetres
        -String provider
        +GeoPoint(lat, lon)
        +distanceTo(other: GeoPoint) Float
        +isValid() Boolean
        +isMockLocation() Boolean
        +toWKT() String
    }

    %% =======================
    %% 3. ORGANIZATION & EVENTS
    %% =======================
    class Organization {
        +UUID orgID
        +String name
        +String regNumber
        +Enum verificationStatus
        +recruitMember(user: User, role: Enum)
        +issueCertificate(vol: Volunteer) Certificate
    }

    class Event {
        +UUID eventID
        +UUID orgID
        +String title
        -GeoPoint location
        -Int geofenceRadius
        -JSON settings
        +addSkillRequirement(skill: Skill, isMandatory: Boolean)
        +isUserInGeofence(userLoc: GeoPoint) Boolean
        +getAvailableShifts() List~Shift~
    }

    %% =======================
    %% 4. SKILLS TAXONOMY
    %% =======================
    class Skill {
        +Int skillID
        +String name
    }

    class VolunteerSkill {
        +Int skillID
        +UUID volunteerID
        +Enum proficiency
    }

    class EventSkill {
        +Int skillID
        +UUID eventID
        +Boolean isMandatory
    }

    %% =======================
    %% 5. OPERATIONAL LOOP
    %% =======================
    class Shift {
        +UUID shiftID
        +DateTime startTime
        +DateTime endTime
        -Int capacity
        -Int filledCount
        +isFull() Boolean
        +reserveSpot() Boolean
    }

    class ShiftApplication {
        +UUID appID
        +UUID shiftID
        +UUID volunteerID
        +Enum status
        +approve()
        +reject(reason: String)
    }

    class TimeLog {
        +UUID logID
        +DateTime checkInTime
        -GeoPoint checkInLocation
        +Enum anomalyFlag
        +UUID verifiedByMemberID
        +calculateDuration() Float
        +detectAnomaly(eventGeo: GeoPoint, radius: Int) Boolean
        +markVerified(member: OrgMember)
    }

    class Certificate {
        +UUID certID
        +String uniqueHash
        +String pdfUrl
        +DateTime generatedDate
        +validateHash() Boolean
        +download() File
    }

    %% =======================
    %% RELATIONSHIP DEFINITIONS
    %% =======================
    
    %% User Roles (Composition)
    User "1" --> "0..1" Volunteer : Has Profile
    User "1" --> "0..n" OrgMember : Works As

    %% Organization Structure
    Organization "1" o-- "n" OrgMember : Employs
    Organization "1" *-- "n" Event : Hosts

    %% Event Structure
    Event "1" *-- "n" Shift : Contains
    Event "1" o-- "n" EventSkill : Requires
    Event *-- "1" GeoPoint : Located At

    %% Volunteer Behavior
    Volunteer "1" o-- "n" VolunteerSkill : Possesses
    Volunteer "1" --> "n" ShiftApplication : Submits
    Volunteer "1" --> "n" TimeLog : Generates
    
    %% Skill References
    EventSkill --> Skill : References
    VolunteerSkill --> Skill : References

    %% Business Flow
    Shift "1" --> "n" ShiftApplication : Receives
    Shift "1" --> "n" TimeLog : Tracks
    
    %% Verification & Geo Logic
    TimeLog *-- "1" GeoPoint : Captured At
    OrgMember ..> TimeLog : Verifies
    
    %% Output
    Certificate ..> Volunteer : Awards to
    Certificate ..> Organization : Issued by
    
    %% Permission Enums
    class Role {
        <<enumeration>>
        OWNER
        ADMIN
        STAFF
    }
    
    class VerificationStatus {
        <<enumeration>>
        UNVERIFIED
        PENDING
        VERIFIED
        SUSPENDED
    }

    %% Process Enums
    class ShiftAppStatus {
        <<enumeration>>
        PENDING
        APPROVED
        REJECTED
        CANCELLED
        WAITLISTED
    }

    class AnomalyFlag {
        <<enumeration>>
        NONE
        GEO_MISMATCH
        DEVICE_MOCK
        TIME_MISMATCH
    }

    %% Attribute Enums
    class Proficiency {
        <<enumeration>>
        BEGINNER
        INTERMEDIATE
        EXPERT
    }

    %% Relationships illustrating usage
    OrgMember ..> Role
    Organization ..> VerificationStatus
    ShiftApplication ..> ShiftAppStatus
    TimeLog ..> AnomalyFlag
    VolunteerSkill ..> Proficiency
```
