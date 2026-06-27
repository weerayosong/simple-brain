---
title: "Spring Boot N-Tier Architecture"
type: wiki-entry
project: "clinical-triage"
createdAt: 2026-06-28T02:50:00
modifiedAt: 2026-06-28T02:50:00
target_agents:
  - backend
  - architect
related_tools:
  - spring-boot
  - java
tags:
  - architecture-pattern
  - backend
---

# Spring Boot N-Tier Architecture (Controller-Service-Repository)

> **🤖 Agent Routing:** Backend agents should read this to understand the standard Separation of Concerns (SoC) used in Spring Boot applications.

> **🧠 LLM Context:** This document explains the N-Tier layered architecture extracted from the Clinical Triage backend. It details why business logic is isolated in the Service layer, rather than being tangled in the Controller.

## 📌 The Architecture Pattern
Spring Boot applications are traditionally built using a 3-Tier architecture. This ensures the codebase remains modular, testable, and scalable.

### 1. The Controller Layer (`@RestController`)
**Role:** The boundary/doorway to the application.
**Responsibilities:**
- Define API endpoints (`@GetMapping`, `@PostMapping`).
- Extract data from the HTTP Request (Params, Body).
- Return the HTTP Response (`ResponseEntity`) with correct status codes.
- **Rule:** *NEVER* put business logic (if/else rules) or database calls directly in here.

```java
@RestController
@RequestMapping("/api/v1/patients")
public class PatientController {
    private final PatientService patientService; // Injected dependency

    @PostMapping
    public ResponseEntity<Patient> registerPatient(@RequestBody Patient patient) {
        // Just routing the request to the Service layer
        Patient savedPatient = patientService.registerPatient(patient);
        return new ResponseEntity<>(savedPatient, HttpStatus.CREATED);
    }
}
```

### 2. The Service Layer (`@Service`)
**Role:** The brain of the application.
**Responsibilities:**
- Contains all Business Logic (e.g., "A patient cannot be discharged if they haven't been treated").
- Coordinates between multiple Repositories if needed.
- Throws appropriate Exceptions if rules are violated.

```java
@Service
public class PatientService {
    private final PatientRepository patientRepository; // Injected dependency

    public Patient updatePatientStatus(Long id, Patient.Status newStatus) {
        // Business logic: Check if patient exists first
        Patient patient = patientRepository.findById(id)
            .orElseThrow(() -> new IllegalArgumentException("Patient not found"));
            
        patient.setStatus(newStatus);
        return patientRepository.save(patient); // Pass to DB layer
    }
}
```

### 3. The Repository Layer (`@Repository` / `JpaRepository`)
**Role:** The database communicator.
**Responsibilities:**
- Handle CRUD operations via Spring Data JPA.
- Abstract away SQL queries into method names (e.g., `findAllByOrderByStatusAsc`).

```java
// Spring Boot generates the implementation automatically!
public interface PatientRepository extends JpaRepository<Patient, Long> {
    List<Patient> findAllByOrderByStatusAscRegisteredAtAsc();
}
```

## ✨ Why this matters
By injecting dependencies via the Constructor (Constructor Injection), we can easily mock the `PatientRepository` when writing Unit Tests for the `PatientService`. If the Controller talked directly to the Repository, testing the application logic would require a real database connection.

## 🔗 Related Context
- [[clinical-triage-architecture]]
