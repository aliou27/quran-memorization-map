````markdown
# Quran Memorization Map

A Quran memorization and revision tracking system designed to help learners organize, assess, and visualize their Quran memorization progress.

The system treats a passage or Quranic range as the fundamental unit of memorization rather than using the Juz or Surah as the smallest unit.

## Project Status

Early development.

The project currently contains the initial Spring Boot backend and PostgreSQL development environment.

The Quran domain model, REST API, and frontend are under development.

## Goals

The system is designed to help users:

- Track Quran memorization progress
- Track memorization at passage/range level
- Distinguish between new, partial, familiar, strong, mastered, and forgotten material
- Track reading ability separately from memorization ability
- Manage currently memorized passages
- Schedule and track revisions
- Reactivate previously learned material
- Visualize memorization progress across the Quran
- Test memorization through random passage selection
- Track Khatm cycles independently from memorization
- Monitor overall progress and statistics

## Architecture

The application follows a client-server architecture.

```text
Quran Memorization Map
        |
        +--------------------+
        |                    |
    Frontend              Backend
        |                    |
  User Interface          REST API
                             |
                        Controllers
                             |
                          Services
                             |
                       Domain Logic
                             |
                        Repositories
                             |
                         Database
````

The responsibilities are separated as follows:

* Frontend: user interface and interaction
* Controllers: HTTP/API entry points
* Services: application use cases and orchestration
* Domain: Quran and memorization business rules
* Repositories: persistence and database access
* Database: persistent application data

The backend owns the core memorization and revision rules so that the same business logic can later be reused by different clients.

## Project Structure

```text
quran-memorization-map/
├── README.md
├── .gitignore
│
├── backend/
│   ├── pom.xml
│   ├── mvnw
│   ├── mvnw.cmd
│   ├── docker-compose.yml
│   │
│   └── src/
│       ├── main/
│       │   ├── java/
│       │   │   └── org/quranmap/backend/
│       │   │       └── BackendApplication.java
│       │   │
│       │   └── resources/
│       │       └── application.yaml
│       │
│       └── test/
│           └── java/
│               └── org/quranmap/backend/
│                   └── BackendApplicationTests.java
│
└── frontend/
    └── planned
```

As development progresses, the backend will be organized around the application domain.

The intended backend structure is:

```text
backend/src/main/java/org/quranmap/backend/

├── controller/
├── service/
├── domain/
│   ├── quran/
│   ├── memorization/
│   ├── revision/
│   └── khatm/
└── repository/
```

These packages will be introduced as the corresponding features are implemented.

## Core Domain

The central concept is the Passage.

```text
Quran
  |
  +-- Surah
       |
       +-- Ayah
            |
            +-- Passage
                 |
                 +-- Memorization
                 +-- Reading
                 +-- Revision
```

A passage represents a continuous Quranic range.

Example:

```text
Surah: Al-Baqarah
Start Ayah: 270
End Ayah: 272
Juz: 3
```

Reading ability, memorization strength, and revision state are separate concepts.

## Memorization Status

The system distinguishes between:

* New
* Partial
* Familiar
* Strong
* Mastered
* Previously studied / forgotten

Previously learned material is not treated as completely new material.

## Reading vs Memorization

Reading ability is tracked separately from memorization ability.

For example:

```text
Reading ability:       95%
Memorization strength: 20%
```

A passage with strong reading ability but weak memorization must not be considered memorized.

## Mastery

Each passage may have a numerical mastery value from 0 to 100.

The numerical value supports progress measurement and visualization, while the qualitative status remains more important.

Example:

```text
0–19    New
20–39   Previously studied / weak
40–59   Partial
60–79   Familiar
80–94   Strong
95–100  Mastered
```

## Revision

Revision is independent from memorization status.

A passage can be strong in memory while still being due for revision.

The initial spaced-revision schedule is:

```text
Day 0
Day 1
Day 3
Day 7
Day 14
Day 30
Day 60
Day 90
```

Failed reviews should result in earlier revision and reduced strength.

## Active Memorization

The system supports a small active set of approximately 3–5 passages currently being memorized.

Example:

```text
Currently Memorizing

1. Al-Baqarah 270–272
2. Al-Baqarah 273–275
3. Maryam 16–18
```

## Reactivation

Previously learned material can enter a reactivation workflow instead of being treated as completely new.

```text
Previously Learned
        |
        v
     Weakens
        |
        v
  Reactivation Queue
        |
        v
     Review
        |
        v
 Relearn / Strengthen
```

Forgetting should not be treated as starting from zero.

## Non-Linear Memorization

The system supports memorization across different parts of the Quran without requiring sequential progression.

Example:

```text
Al-Baqarah 270–276     Currently memorizing

Al-Kahf 1–10           Strong

Maryam 16–25           Familiar

Yasin 1–20             Previously learned / weak
```

The system therefore cannot assume that memorization always progresses from the beginning of the Quran toward the end.

## Khatm

Khatm tracking is separate from memorization.

A Khatm cycle represents reading the Quran from beginning to end according to a reading schedule.

The system will support:

* Khatm cycles
* Daily reading targets
* Current Khatm position
* Progress tracking
* Adaptive pace

## Testing

The system will support:

* Passage recall
* Random passage testing
* Weak-area identification
* Reactivation testing

## Technology Stack

### Backend

* Java 21
* Spring Boot
* Spring Data JPA
* Hibernate
* PostgreSQL
* Maven

### Development Environment

* Docker
* Docker Compose

### Frontend

Planned.

The frontend will communicate with the backend through a REST API.

## Requirements

* Java 21 or later
* Docker Desktop
* Git

A local PostgreSQL installation is not required.

PostgreSQL is provided through Docker Compose.

## Running the Backend

Clone the repository:

```bash
git clone https://github.com/aliou27/quran-memorization-map.git
cd quran-memorization-map/backend
```

Start PostgreSQL:

```bash
docker compose up -d
```

Start Spring Boot:

```bash
./mvnw spring-boot:run
```

The backend runs on:

```text
http://localhost:8080
```

## Database

The development database is created by Docker Compose.

Database configuration is defined by the application's development configuration and Docker Compose environment.

Do not commit production credentials, passwords, API keys, or other secrets to the repository.

## Stopping the Development Environment

Stop the database:

```bash
docker compose down
```

To remove the database and its local data:

```bash
docker compose down -v
```

Warning: removing the volume deletes the local development database.

## Development Principles

The project aims to remain:

* Simple
* Maintainable
* Testable
* Modular
* Reusable

Business rules should not be implemented directly in controllers or duplicated in the frontend.

The backend should remain the source of truth for memorization, revision, and Khatm business logic.

The project should avoid unnecessary complexity such as microservices when a modular monolith is sufficient.

## Roadmap

### Phase 1 — Foundation

* [x] Spring Boot project
* [x] PostgreSQL Docker environment
* [x] Spring Boot to PostgreSQL connection
* [x] Git repository
* [x] GitHub repository
* [x] Project documentation
* [ ] Domain model

### Phase 2 — Quran Domain

* [ ] Quran structure
* [ ] Surah
* [ ] Ayah
* [ ] Passage/range
* [ ] Passage selection

### Phase 3 — Memorization

* [ ] Memorization status
* [ ] Mastery score
* [ ] Reading status
* [ ] Active memorization
* [ ] Passage editing
* [ ] Passage splitting

### Phase 4 — Revision

* [ ] Revision history
* [ ] Revision scheduling
* [ ] Spaced revision
* [ ] Failed-review handling
* [ ] Reactivation queue

### Phase 5 — Testing

* [ ] Passage recall testing
* [ ] Random passage testing
* [ ] Weak-area identification

### Phase 6 — Khatm

* [ ] Khatm cycles
* [ ] Daily reading targets
* [ ] Progress tracking
* [ ] Adaptive pace

### Phase 7 — Frontend

* [ ] Dashboard
* [ ] Quran map
* [ ] Passage management
* [ ] Revision interface
* [ ] Testing mode
* [ ] Khatm interface
* [ ] Statistics

## License


```
```
