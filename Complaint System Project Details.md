# Smart College Complaint Management System

An AI-powered complaint management system built using **n8n**, **Gemini Vision API**, **PostgreSQL**, **Telegram**, and **Email integrations** to automate complaint triaging and ticket handling for college facilities.

---

## Problem Context

College administrations often receive facility complaints through multiple channels such as email, WhatsApp, and paper forms. These complaints are typically unstructured and require manual review before being assigned to the appropriate maintenance team.

Common issues include:

* Complaints getting lost or overlooked
* Incorrect department assignment
* Delayed response times
* Lack of centralized tracking
* Manual follow-up processes

This project automates the complaint triaging process by using AI to analyze complaint information and route tickets to the correct department.

---

## System Overview

The system receives complaint submissions containing:

* Text descriptions
* Optional image attachments

Using an n8n workflow, the complaint is processed through Gemini Vision for classification and then stored as a structured ticket.

The workflow automatically:

* Categorizes complaints
* Determines issue severity
* Assigns the responsible department
* Creates a ticket record
* Sends Telegram notifications
* Sends Email notifications
* Tracks ticket status
* Escalates unresolved complaints

---

## Architecture

```text
Student Complaint
(Image + Description)
          │
          ▼
     n8n Webhook
          │
          ▼
    n8n Workflow
          │
          ▼
 Gemini Vision API
(Image Analysis)
          │
          ▼
 Complaint Category
 + Severity Level
          │
          ▼
 Department Routing
          │
          ▼
 PostgreSQL Storage
          │
    ┌─────┴─────┐
    ▼           ▼
Telegram      Email
Alerts        Alerts
```

---

## Complaint Processing Flow

```text
Webhook Trigger
       │
       ▼
Receive Form Data
(Image + Text)
       │
       ▼
Gemini Vision Analysis
       │
       ▼
Generate Classification
       │
       ▼
Determine Severity
       │
       ▼
Route To Department
       │
       ▼
Store Ticket
       │
       ▼
Send Notifications
```

---

## Database Design

### Complaints Table

Stores complaint metadata including:

* Ticket ID
* Description
* Category
* Severity
* Department
* Status
* Timestamps

### StatusLog Table

Tracks ticket status changes through the complaint lifecycle.

```text
Complaints
     │
     │ 1 : N
     ▼
StatusLog
```

### Ticket Lifecycle

```text
Open
 │
 ▼
Triaged
 │
 ▼
In Progress
 │
 ▼
Resolved
```

Each status transition is recorded in the StatusLog table.

---

## Core Features

### Automated Complaint Categorization

Uses Gemini API to classify submitted complaints into maintenance categories.

### Image-Based Analysis

Analyzes uploaded images to assist in identifying infrastructure issues.

### Department Assignment

Routes complaints to the appropriate maintenance department based on classification results.

### Real-Time Notifications

Sends Telegram and Email notifications when new tickets are created.

### Ticket Tracking

Stores complaint status and maintains a history of status changes.

### Automated Escalation

Supports escalation workflows for unresolved complaints.

---

## Engineering Decisions

### Workflow Orchestration

Used n8n as the orchestration layer to manage API calls, routing logic, database operations, and notifications within a single workflow.

### Database Design

Implemented a normalized PostgreSQL schema using separate tables for complaint records and status history tracking.

### Notification Strategy

Telegram and Email notifications are triggered asynchronously after ticket creation.

---

## Security Measures

* Secret token verification on incoming webhooks
* Input sanitization before database insertion
* Controlled access to workflow credentials

---

## Implementation Challenges

### Image Classification Accuracy

Handling visually similar scenarios such as:

* Broken pipes
* Water spills
* Infrastructure damage

### External API Reliability

Managing:

* Gemini API rate limits
* Request timeouts
* Error responses

### Workflow Resilience

Designing error-handling paths to prevent workflow failures and ensure ticket processing continuity.

---

## Scaling Considerations

The current implementation performs Gemini analysis synchronously within the n8n workflow.

For larger deployments, the AI processing stage could be moved behind a message queue such as RabbitMQ:

```text
Webhook
   │
   ▼
RabbitMQ
   │
   ▼
Gemini Worker
   │
   ▼
PostgreSQL
```

This would reduce webhook response times and improve handling of traffic spikes.

---

## Technology Stack

| Component           | Technology           |
| ------------------- | -------------------- |
| Workflow Automation | n8n                  |
| AI Analysis         | Gemini Vision API    |
| Database            | PostgreSQL           |
| Notifications       | Telegram API         |
| Email Service       | SMTP                 |
| Data Processing     | JSON-based Workflows |

---

