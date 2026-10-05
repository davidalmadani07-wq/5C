\# Database Design - Entity Relationship Diagram



This document describes the database schema of \*\*Minisoccer\*\* and every Eloquent relationship used in the project.



\## 1. Entity Relationship Diagram



```mermaid

erDiagram

&#x20;   USERS ||--o| PROFILES : "has one"

&#x20;   USERS ||--o{ TEAMS : "owns"

&#x20;   USERS ||--o{ BOOKINGS : "makes"

&#x20;   VENUES ||--o{ FIELDS : "has"

&#x20;   FIELDS ||--o{ BOOKINGS : "booked in"

&#x20;   FIELDS ||--o{ FACILITY\_FIELD : "equipped via"

&#x20;   FACILITIES ||--o{ FACILITY\_FIELD : "provided via"

&#x20;   TEAMS ||--o{ PLAYERS : "has"

&#x20;   TEAMS ||--o{ TEAM\_TOURNAMENT : "joins via"

&#x20;   TOURNAMENTS ||--o{ TEAM\_TOURNAMENT : "hosts via"



&#x20;   USERS {

&#x20;       bigint id PK

&#x20;       string name

&#x20;       string email UK

&#x20;       string password

&#x20;   }

&#x20;   PROFILES {

&#x20;       bigint id PK

&#x20;       bigint user\_id FK "unique"

&#x20;       string phone

&#x20;       string address

&#x20;       string avatar

&#x20;   }

&#x20;   VENUES {

&#x20;       bigint id PK

&#x20;       string name

&#x20;       string address

&#x20;       string city

&#x20;   }

&#x20;   FIELDS {

&#x20;       bigint id PK

&#x20;       bigint venue\_id FK

&#x20;       string name

&#x20;       string surface

&#x20;       int price\_per\_hour

&#x20;       boolean is\_active

&#x20;   }

&#x20;   FACILITIES {

&#x20;       bigint id PK

&#x20;       string name UK

&#x20;       string icon

&#x20;   }

&#x20;   FACILITY\_FIELD {

&#x20;       bigint field\_id FK

&#x20;       bigint facility\_id FK

&#x20;   }

&#x20;   TEAMS {

&#x20;       bigint id PK

&#x20;       bigint user\_id FK

&#x20;       string name

&#x20;       string logo

&#x20;   }

&#x20;   PLAYERS {

&#x20;       bigint id PK

&#x20;       bigint team\_id FK

&#x20;       string name

&#x20;       tinyint jersey\_number

&#x20;       string position

&#x20;   }

&#x20;   TOURNAMENTS {

&#x20;       bigint id PK

&#x20;       string name

&#x20;       date start\_date

&#x20;       date end\_date

&#x20;       int registration\_fee

&#x20;   }

&#x20;   TEAM\_TOURNAMENT {

&#x20;       bigint team\_id FK

&#x20;       bigint tournament\_id FK

&#x20;       timestamp registered\_at

&#x20;       string status

&#x20;       string group\_name

&#x20;   }

&#x20;   BOOKINGS {

&#x20;       bigint id PK

&#x20;       bigint user\_id FK

&#x20;       bigint field\_id FK

&#x20;       date booking\_date

&#x20;       time start\_time

&#x20;       time end\_time

&#x20;       int total\_price

&#x20;       string status

&#x20;   }

```



\## 2. Relationship Summary



| Type | Relationship | Eloquent |

|---|---|---|

| One-to-One | `User` and `Profile` | `hasOne` / `belongsTo` |

| One-to-Many | `Venue` to `Field` | `hasMany` / `belongsTo` |

| One-to-Many | `User` to `Team` | `hasMany` / `belongsTo` |

| One-to-Many | `Team` to `Player` | `hasMany` / `belongsTo` |

| One-to-Many | `User` to `Booking` | `hasMany` / `belongsTo` |

| One-to-Many | `Field` to `Booking` | `hasMany` / `belongsTo` |

| Many-to-Many | `Field` and `Facility` (pivot `facility\_field`) | `belongsToMany` |

| Many-to-Many + pivot data | `Team` and `Tournament` (pivot `team\_tournament`, columns `registered\_at`, `status`, `group\_name`) | `belongsToMany` + `withPivot` |

| Has-Many-Through | `Venue` to `Booking` through `Field` | `hasManyThrough` |

| Has-Many-Through | `User` to `Player` through `Team` | `hasManyThrough` |



\## 3. Design Notes



\- \*\*Unique constraints:\*\* `profiles.user\_id`, `facilities.name`, and (`team\_tournament.team\_id`, `team\_tournament.tournament\_id`) so a team joins a tournament only once.

\- \*\*Cascade rules:\*\* deleting a user cascades to their profile, teams, and bookings. Deleting a venue is restricted while fields still exist.

