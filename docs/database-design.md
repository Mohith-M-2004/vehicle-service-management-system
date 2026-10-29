# VSMS Database Design

**Project:** Vehicle Service Management System
**Version:** Draft 1.0
**Status:** Proposed design for review

## 1. Purpose

This document defines the proposed relational database design for VSMS. It identifies the main entities, their relationships, data integrity rules, and important business constraints before implementation begins.

The database will use MySQL, with Spring Data JPA and Hibernate handling persistence in the Spring Boot backend.

The design prioritises data integrity, maintainability, security, and the preservation of historical service and financial records.

## 2. Design principles

1. Use relational tables with primary keys and foreign keys.
2. Separate authentication accounts from customer and employee profiles.
3. Keep appointments separate from service jobs.
4. Keep estimates separate from invoices and payments.
5. Preserve historical prices and financial records.
6. Enforce permissions and business rules in the backend.
7. Use transactions for operations that must succeed or fail together.
8. Avoid unnecessary complexity while keeping the design extensible.
9. Do not delete business records merely because their status changes.
10. Validate relationships and constraints before implementing JPA entities.

## 3. Entity catalogue

### 3.1 Authentication and profiles

#### `users`
Stores application accounts and authentication-related information.

Proposed fields:
- `user_id` (PK)
- `email` (unique)
- `password_hash`
- `role`
- `account_status`
- `created_at`
- `updated_at`

The role identifies whether the account belongs to an Administrator, Workshop Staff member, or Customer. Passwords must be stored as secure hashes, never plaintext.

#### `customers`
Stores customer business and contact information.

Proposed fields:
- `customer_id` (PK)
- `user_id` (nullable, unique FK to `users.user_id`)
- `first_name`
- `last_name`
- `phone`
- `address`
- `created_at`
- `updated_at`

A customer profile may exist without an online account, supporting walk-in customers and staff-created records. A later account must be linked only after appropriate identity verification.

#### `employees`
Stores workshop employee profiles.

Proposed fields:
- `employee_id` (PK)
- `user_id` (nullable, unique FK to `users.user_id`)
- `first_name`
- `last_name`
- `phone`
- `job_title`
- `employment_status`
- `created_at`
- `updated_at`

Employee records are separate from login accounts. More detailed staff permissions may be introduced if different workshop responsibilities require them.

### 3.2 Vehicles and appointments

#### `vehicles`
Stores vehicles registered with the workshop.

Proposed fields:
- `vehicle_id` (PK)
- `customer_id` (FK to `customers.customer_id`)
- `registration_number`
- `make`
- `model`
- `manufacture_year`
- `created_at`
- `updated_at`

Each vehicle belongs to one customer. A customer may own multiple vehicles. Every vehicle must reference a valid customer.

Registration-number uniqueness and normalisation rules must be finalised before implementation.

#### `appointments`
Stores requested and scheduled workshop visits.

Proposed fields:
- `appointment_id` (PK)
- `customer_id` (FK)
- `vehicle_id` (FK)
- `requested_at`
- `scheduled_at`
- `status`
- `customer_notes`
- `cancellation_reason` (nullable)
- `created_at`
- `updated_at`

Every booking requires staff approval. Appointment statuses may include `REQUESTED`, `CONFIRMED`, `REJECTED`, `CANCELLED`, `COMPLETED`, and `NO_SHOW`.

Appointments are retained after cancellation, with their status updated instead of deleting the record. Cancellation eligibility and scheduling conflict checks must be enforced by the backend.

### 3.3 Service management

#### `service_jobs`
Represents actual work undertaken by the workshop.

Proposed fields:
- `service_job_id` (PK)
- `appointment_id` (nullable FK to `appointments.appointment_id`)
- `customer_id` (FK)
- `vehicle_id` (FK)
- `assigned_employee_id` (nullable FK to `employees.employee_id`)
- `job_number` (unique)
- `reported_problem`
- `inspection_notes`
- `status`
- `opened_at`
- `completed_at` (nullable)
- `created_at`
- `updated_at`

A service job is separate from an appointment. Walk-in jobs are supported by allowing `appointment_id` to be null.

For Version 1, the proposed rule is at most one primary service job per appointment. The database and service layer must prevent inconsistent customer, vehicle, and appointment associations.

#### `service_catalogue`
Stores standard services offered by the workshop.

Proposed fields:
- `service_id` (PK)
- `service_name`
- `description`
- `default_labour_price`
- `estimated_duration_minutes`
- `is_active`

Catalogue prices and durations may change over time. Historical estimates and invoices must retain their own price snapshots.

#### `job_tasks`
Records work items associated with a service job.

Proposed fields:
- `job_task_id` (PK)
- `service_job_id` (FK)
- `service_id` (nullable FK to `service_catalogue.service_id`)
- `description`
- `labour_price_at_time`
- `status`
- `created_at`
- `completed_at` (nullable)

The description and applicable price are preserved with the job. A task may be linked to a standard catalogue service or represent custom work.

### 3.4 Estimates and approvals

#### `estimates`
Stores estimates prepared for a service job.

Proposed fields:
- `estimate_id` (PK)
- `service_job_id` (FK)
- `version_number`
- `status`
- `subtotal`
- `tax_amount`
- `discount_amount`
- `total_amount`
- `created_at`
- `approved_at` (nullable)
- `rejected_at` (nullable)

The combination of `service_job_id` and `version_number` must be unique.

Estimate statuses may include `DRAFT`, `PENDING_APPROVAL`, `APPROVED`, `REJECTED`, and `SUPERSEDED`.

Revised estimates must be stored as new versions rather than overwriting previously issued versions. Customer approval must be recorded, and additional repair work cannot proceed until the revised estimate is approved.

#### `estimate_items`
Stores individual items within an estimate.

Proposed fields:
- `estimate_item_id` (PK)
- `estimate_id` (FK)
- `item_type`
- `description`
- `quantity`
- `unit_price`
- `tax_rate`
- `line_total`

Descriptions, quantities, and prices preserve the values presented in the relevant estimate. Money fields use suitable decimal types, not floating-point types.

### 3.5 Invoices and payments

#### `invoices`
Stores billing records for completed or otherwise billable work.

Proposed fields:
- `invoice_id` (PK)
- `service_job_id` (FK)
- `invoice_number` (unique)
- `status`
- `subtotal`
- `tax_amount`
- `discount_amount`
- `total_amount`
- `issued_at`
- `due_at` (nullable)

An invoice is a separate financial record, not a replacement for an estimate. Issued invoice amounts and item details must be preserved. Corrections must use a controlled process.

The initial assumption is one final invoice per service job. The need for supplementary invoices or credit notes will be reviewed before billing implementation.

#### `invoice_items`
Stores the individual charges on an invoice.

Proposed fields:
- `invoice_item_id` (PK)
- `invoice_id` (FK)
- `item_type`
- `description`
- `quantity`
- `unit_price`
- `tax_rate`
- `line_total`

Invoice items preserve historical descriptions, quantities, and prices independently of future catalogue changes.

#### `payments`
Stores payments recorded by authorised workshop staff.

Proposed fields:
- `payment_id` (PK)
- `invoice_id` (FK)
- `amount`
- `payment_method`
- `payment_reference` (nullable)
- `paid_at`
- `recorded_by_user_id` (FK to `users.user_id`)
- `status`
- `created_at`

An invoice may have multiple payments. Partial payments and outstanding balances are supported. Version 1 uses staff-recorded payments rather than an external payment gateway.

Payment corrections, failed payments, and refunds require controlled handling. Payment records must not be casually overwritten or deleted.

### 3.6 Inventory

#### `parts`
Stores spare-part catalogue and current stock information.

Proposed fields:
- `part_id` (PK)
- `sku` (unique)
- `part_name`
- `description`
- `unit_cost`
- `selling_price`
- `stock_quantity`
- `reorder_level`
- `is_active`

Stock quantities must not become negative unless an explicitly approved business rule permits it. Stock changes must be validated and performed transactionally.

#### `job_parts`
Records parts consumed by service jobs.

Proposed fields:
- `job_part_id` (PK)
- `service_job_id` (FK)
- `part_id` (FK)
- `quantity_used`
- `unit_price_at_use`
- `created_at`

This resolves the many-to-many relationship between service jobs and parts. A job may use multiple parts, and the same part may be used across many jobs.

Recording part usage and reducing stock must happen consistently in a database transaction. A separate inventory-movement ledger may be introduced if required for detailed purchase, return, adjustment, and stock-reconciliation history.

### 3.7 Supporting records

#### `notifications`
Stores in-app notifications.

Proposed fields:
- `notification_id` (PK)
- `user_id` (FK)
- `title`
- `message`
- `notification_type`
- `is_read`
- `created_at`

Notifications provide updates about booking decisions, estimate approvals, job progress, and invoices. They are informational records, not substitutes for the underlying business records.

#### `audit_logs`
Records significant administrative and business actions.

Proposed fields:
- `audit_log_id` (PK)
- `user_id` (nullable FK)
- `action`
- `entity_type`
- `entity_id`
- `details`
- `created_at`

Audit logs may record important events such as booking decisions, estimate approvals, invoice issuance, payment recording, and stock adjustments. Sensitive information such as passwords and authentication tokens must not be logged.

## 4. Relationship summary

| Relationship | Cardinality and rule |
|---|---|
| `users` to `customers` | Optional one-to-one link |
| `users` to `employees` | Optional one-to-one link |
| `customers` to `vehicles` | One-to-many |
| `customers` to `appointments` | One-to-many |
| `vehicles` to `appointments` | One-to-many |
| `appointments` to `service_jobs` | Optional association; at most one primary job per appointment in Version 1 |
| `customers` to `service_jobs` | One-to-many |
| `vehicles` to `service_jobs` | One-to-many |
| `employees` to `service_jobs` | One-to-many assignment; assignment may be empty |
| `service_jobs` to `job_tasks` | One-to-many |
| `service_catalogue` to `job_tasks` | One-to-many; task link may be empty |
| `service_jobs` to `estimates` | One-to-many for versioned estimates |
| `estimates` to `estimate_items` | One-to-many |
| `service_jobs` to `invoices` | One-to-many structurally; initial business rule assumes one final invoice per job |
| `invoices` to `invoice_items` | One-to-many |
| `invoices` to `payments` | One-to-many |
| `service_jobs` to `job_parts` | One-to-many |
| `parts` to `job_parts` | One-to-many |
| `users` to `notifications` | One-to-many |
| `users` to `audit_logs` | One-to-many; system-generated events may have no user |

## 5. Integrity and security rules

1. Use primary keys for every entity and foreign keys for relationships.
2. Apply unique constraints to account emails, job numbers, invoice numbers, part SKUs, estimate versions per job, and any registration numbers determined to be globally unique.
3. Use `NOT NULL` for mandatory relationships and allow nulls only when the business workflow supports them.
4. Validate that a vehicle belongs to the customer associated with an appointment or service job.
5. Validate that customers can access only their own authorised records.
6. Enforce roles and permissions in Spring Boot, never by trusting frontend-supplied roles.
7. Hash passwords securely and protect credentials through environment-based configuration.
8. Use database transactions for related changes, including stock deduction and payment recording.
9. Preserve issued financial records and estimate versions. Use explicit status transitions rather than destructive replacement.
10. Validate amounts, quantities, status transitions, and discount permissions in the backend.
11. Use appropriate decimal precision for monetary values and suitable timestamps for business events.
12. Define deletion and retention policies before implementing operations that remove business records.

## 6. Decisions still requiring confirmation

The following details must be finalised before their respective modules are implemented:

- **Vehicle registration:** whether registration numbers must be unique across the workshop and how they are normalised.
- **Scheduling:** operating hours, slot duration, closure dates, workshop capacity, and conflict handling.
- **Appointments:** cancellation cutoff and rules for rescheduling or reinstating a booking.
- **Service jobs:** assignment rules, status transitions, and job completion verification.
- **Estimates:** who can approve on behalf of a customer, how approval evidence is recorded, and how revised estimates affect existing work.
- **Billing:** tax configuration, authorised discount limits, invoice issue timing, and whether supplementary invoices or credit notes are needed.
- **Payments:** permitted methods, payment status transitions, refund handling, and outstanding-balance calculation.
- **Inventory:** purchasing, supplier records, returns, damaged parts, stock adjustments, and low-stock handling.
- **Account linking:** the identity-verification process for connecting an online account to an existing customer profile.
- **Audit and retention:** which actions require audit entries and how long relevant records must be retained.

These decisions do not all block the initial logical design, but they must be resolved before implementing the affected workflows.

## 7. Implementation approach

After this design is reviewed and approved:

1. Finalise the table relationships and constraints.
2. Review the existing Spring Boot and MySQL configuration.
3. Decide how schema changes will be managed as development progresses.
4. Implement the first backend module in small steps, starting with the foundational account and customer model when the authentication approach is agreed.
5. Test repository and service behaviour, then verify REST endpoints using Postman.
6. Integrate the React frontend after the relevant API contracts are established.
7. Add automated tests and update the project documentation as each workflow is completed.

**Current status:** this document is a proposed logical database design. No business tables or JPA entities have been created as part of preparing it. The existing VSMS database and working backend remain unchanged.
