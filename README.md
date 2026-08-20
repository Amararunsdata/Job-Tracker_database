# Job Application Tracker - Relational Database

A redesign of my personal Excel job application tracker into a relational database using **MySQL** and **MySQL Workbench**.

The original spreadsheet worked well for tracking applications, but its structure had a limitation: it only captured the **latest step** reached in an application process. As a result, previous stages were lost, making it difficult to analyse the full application journey.

This project explores how I could redesign the tracker as a relational database while balancing **normalization, business logic, simplicity, and analytical requirements**.

## Project Overview

The original Excel tracker contained information such as:

* Date
* Job Title
* Company
* Location
* CV Type
* Application Status
* Last Step in Application Process
* Language

The goal was not simply to convert the spreadsheet into tables. Instead, I wanted to reconsider the underlying relationships and business rules.

The resulting model is **almost 3NF by design**, with some deliberate decisions made based on the practical requirements of the application tracker.

## Key Design Decisions

### 1. Company as an attribute

A strict normalization approach could justify creating a separate `Company` entity.

However, most companies in the dataset appear only once. Creating a separate entity would therefore add another relationship without providing much practical value for this use case.

**Decision:** `Company` remains an attribute of `Application`.

This is a deliberate trade-off between normalization and practical simplicity.

### 2. Separating Status and Process

The original spreadsheet used the same validation list for both:

* Application Status
* Last Step in Application Process

These represent different concepts.

**Stage** describes progress through the recruitment process, while **Outcome** describes the result of the application.

The database therefore separates these concepts.

Examples of stages:

* HR / Recruiter Interview
* Technical Assessment
* Technical Interview
* Final Interview

Examples of outcomes:

* Pending
* Rejected
* Offer
* Accepted
* Withdrawn

`Applied` is not stored as a stage because the existence of an `Application` record already implies that an application was submitted.

### 3. Preserving the application journey

The spreadsheet stored only the latest process step. Once an application progressed, its previous stage was effectively overwritten.

To preserve the full journey, the model introduces an `ApplicationStage` associative table between `Application` and `Stage`.

This allows one application to be associated with multiple stages while retaining its history.

The combination of:

`ApplicationID + StageID`

forms the composite primary key of `ApplicationStage`, preventing the same stage from being recorded more than once for an application.

## Data Model

The resulting relational model consists of the core application entity, application-stage history, recruitment stages, outcomes, and controlled reference data.

The final ER diagram is included in this repository.


## Repository Contents

| File                          | Description                          |
| ----------------------------- | ------------------------------------ |
| `JobAppTracker database.mwb`  | MySQL Workbench data model           |
| `job_applications ERD.png`    | Exported Entity Relationship Diagram |
| `README.md`                   | Project documentation                |

## Tools

* **MySQL**
* **MySQL Workbench**
* **Excel** — original tracking system and source structure

## Key Learning

The main lesson from this exercise was that database design involves more judgement than I initially expected.

Normalization provides useful principles, but applying those principles to a real-world process requires consideration of:

* Business requirements
* Data usage
* Analytical needs
* Relationships between entities
* Complexity
* Redundancy
* Maintainability

The most interesting part of the project was therefore not producing the final ER diagram, but the reasoning and trade-offs that led to it.

## Future Development

A natural next step is to create a **strict 3NF version** of the model and compare it with this practical design.

The comparison will explore what additional entities and relationships are introduced through stricter normalization, and whether the resulting complexity provides meaningful benefits for this particular use case.
