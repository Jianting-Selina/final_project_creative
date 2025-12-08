# This is ERD.

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
