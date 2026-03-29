# System Architecture

## Overview

The Defect Detection System is built using a **layered architecture** that separates responsibilities across testing, execution, defect processing, storage, and reporting.

This design ensures:

* modularity
* scalability
* clear data flow
* easy extensibility

---

## High-Level Architecture

```text
Test Layer
    ↓
Test Executor
    ↓
Defect Detection Engine
    ↓
Database Layer
    ↓
Reporting Layer
```

---

## 1. Test Layer

### Description

The Test Layer contains all predefined test cases responsible for validating system behavior across different modules.

### Location

```text
tests/
```

### Responsibilities

* Define test scenarios
* Validate business logic and APIs
* Simulate edge cases and failure conditions

### Test Categories

* **Functional Tests** → Logic validation (e.g., calculator operations)
* **API Tests** → Endpoint verification (status codes, responses)
* **Validation Tests** → Input correctness (emails, datasets, etc.)
* **Log Tests** → Detect runtime issues from logs
* **Generic Tests** → System-wide sanity checks

### Key Characteristic

Tests are **decoupled from application logic**, allowing independent evolution of testing and implementation.

---

## 2. Test Executor

### Description

The Test Executor is responsible for discovering, loading, and executing test cases dynamically.

### Location

```text
executor/test_runner.py
```

### Responsibilities

* Discover test modules using `unittest`
* Execute tests per selected project or globally
* Aggregate results:

  * total tests
  * passed
  * failed
  * detailed errors

### Behavior

* Supports **project-specific execution**
* Returns structured results to the UI
* Acts as the **core orchestration engine**

---

## 3. Defect Detection Engine

### Description

This layer processes test execution results and identifies defects based on failures and runtime anomalies.

### Location

```text
executor/defect_detector.py
```

### Responsibilities

Detects defects from:

* **Assertion Failures**

  * Logic mismatch (expected vs actual)

* **HTTP Failures**

  * Invalid status codes (e.g., 404, 500)

* **Runtime Exceptions**

  * Crashes (e.g., division by zero)

* **Log-Based Errors**

  * Extracted from runtime logs (e.g., "ERROR", "CRITICAL")

### Output

Structured defect objects:

```text
{
  test_name,
  defect_type,
  message,
  http_code,
  timestamp
}
```

---

## 4. Database Layer

### Description

Responsible for persistent storage of detected defects.

### Location

```text
database/
```

### Technology

* SQLite (lightweight, file-based)

### Responsibilities

* Store defect records
* Retrieve defect history
* Support clearing/resetting defects

### Data Model

Each defect record includes:

* ID
* Test Name
* Defect Type
* Message
* HTTP Code
* Timestamp

### Key Advantage

Provides **traceability and historical analysis** of system issues.

---

## 5. Reporting Layer

### Description

Transforms stored defect data into exportable formats for analysis and reporting.

### Location

```text
services/report_generator.py
```

### Responsibilities

* Generate CSV reports (tabular format)
* Generate PDF reports (formatted summary)
* Generate Excel reports (structured analytics)

### Output Formats

* `.csv` → Data analysis
* `.pdf` → Presentation/reporting
* `.xlsx` → Advanced inspection

---

## 6. Data Flow

### Execution Pipeline

1. User initiates test execution from the UI
2. Test Executor runs test suite
3. Results passed to Defect Detection Engine
4. Failures converted into defect records
5. Defects stored in SQLite
6. UI fetches and displays results
7. Reports generated on demand

---

## 7. Architectural Strengths

* **Loose Coupling**
  Each layer operates independently

* **Extensibility**
  New tests or modules can be added without modifying core logic

* **Centralized Defect Management**
  All failures are stored and accessible

* **Multi-Layer Validation**
  Covers logic, APIs, inputs, and runtime behavior

---

## 8. Limitations (Current State)

* No real-time streaming of test results
* SQLite limits scalability for high concurrency
* No distributed execution of tests
* Limited defect classification (all treated similarly)

---

## 9. Future Improvements

* Introduce message queue for async test execution
* Replace SQLite with PostgreSQL for scalability
* Add real-time monitoring dashboard (WebSockets)
* Implement severity classification (Critical, Warning, Info)
* Integrate CI/CD pipelines

---

## Conclusion

The system architecture provides a structured and modular foundation for automated defect detection. By separating testing, execution, defect processing, and reporting into distinct layers, it enables maintainability, scalability, and clear system behavior—similar to modern QA automation platforms.
