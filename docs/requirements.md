# Vehicle Service Management System (VSMS)
## Software Requirements Specification

**Document version:** 1.0
**Status:** Final draft for review
**Project type:** Full-stack portfolio project
**Application:** Web-based vehicle service centre management system

---

## 1. Project Overview

The Vehicle Service Management System (VSMS) is a full-stack web application designed to manage the day-to-day operations of a vehicle service centre. It provides a centralised system for customer and vehicle records, appointment booking, service job tracking, estimates, invoicing, payment recording, spare-parts inventory, and operational reporting.

The application will provide role-appropriate interfaces for customers and workshop personnel. Customers will be able to manage their profiles and vehicles, request appointments, review estimates, approve additional repair work, and track service progress. Workshop personnel will manage bookings, service jobs, customers, estimates, invoices, payments, and inventory according to their permissions.

The initial release will support a **single workshop location** and prioritise a complete, reliable service workflow over implementing every possible feature.

## 2. Project Objectives

- Digitise customer, vehicle, appointment, and service-job records.
- Provide a consistent workflow from appointment request to vehicle delivery.
- Improve visibility into service progress, estimates, and outstanding payments.
- Maintain accurate records of labour, parts, taxes, discounts, and payments.
- Track spare-parts stock and record inventory consumption.
- Enforce role-based access and protect customer and financial information.
- Demonstrate practical full-stack development, testing, documentation, and version-control practices.

## 3. Technology Stack

| Layer | Technology |
|---|---|
| Frontend | React |
| Backend | Java 21, Spring Boot, REST API |
| Persistence | Spring Data JPA and Hibernate |
| Database | MySQL |
| API testing | Postman |
| Version control | Git and GitHub |
| Backend IDE | Eclipse |
| Frontend IDE | Visual Studio Code |

The frontend, backend, and database will have clearly separated responsibilities. The React application will communicate with the backend through HTTP APIs. Business rules, validation, authorisation, and financial calculations will be enforced by the backend rather than trusted to the frontend.

## 4. User Roles and Permissions

### 4.1 Administrator

The administrator will be responsible for system-level management, including:

- Managing staff accounts and role assignments.
- Managing service catalogue entries and configurable tax settings.
- Managing discount permissions or approval rules.
- Viewing operational reports and relevant business records.
- Accessing audit history and administrative functions.
- Managing system configuration permitted by the application.

Administrative access must not automatically bypass security controls or data-integrity rules.

### 4.2 Workshop Staff

Workshop staff will perform operational tasks according to their assigned permissions:

- Register customers and maintain customer records.
- Register vehicles and associate them with customers.
- Review, confirm, reject, reschedule, or cancel appointment requests according to the booking rules.
- Register walk-in services without requiring an appointment.
- Create and update service jobs and job cards.
- Assign mechanics and track service progress.
- Prepare estimates and manage customer approvals.
- Create invoices and record workshop payments.
- Manage spare-parts inventory and record parts consumed by jobs.
- View operational dashboards and reports permitted by their role.

Mechanic-specific permissions may be introduced where necessary, rather than granting every staff member unrestricted access.

### 4.3 Customer

Customers will be able to:

- Register an account and sign in securely.
- View and update permitted profile information.
- Register and manage their own vehicles.
- Submit appointment requests and view booking status.
- Cancel eligible bookings according to the cancellation rules.
- View their own service history and service-job status.
- Review estimates and approve or reject them.
- Review revised estimates before additional repair work proceeds.
- View their own invoices, recorded payments, and outstanding balances.
- Receive in-app updates about relevant bookings and services.

Customers must not access another customer's vehicles, appointments, estimates, invoices, or other private records.

## 5. Functional Requirements

### FR-01: Authentication and Access Control

1. The system shall support secure registration and sign-in.
2. Passwords shall be stored as secure password hashes, never as plaintext.
3. The system shall distinguish Administrator, Workshop Staff, and Customer roles.
4. The backend shall enforce authentication and role-based authorisation for protected endpoints.
5. Customer requests shall be checked against the authenticated customer's ownership of the requested resources.
6. Account activation, deactivation, and role changes shall be restricted to authorised users.
7. Authentication and authorisation failures shall return appropriate HTTP status codes without exposing sensitive implementation details.

### FR-02: Customer Management

1. Customers shall be able to create their own accounts.
2. Authorised staff shall be able to create customer records for customers who register at the workshop.
3. Customer records shall contain appropriate contact and profile information.
4. The system shall avoid accidentally creating duplicate customer accounts or linking an existing account to an unauthorised user.
5. Customer information shall be accessible only to authorised users.
6. The system shall support updating permitted customer details and deactivating records where appropriate.

### FR-03: Vehicle Management

1. Customers shall be able to register vehicles associated with their accounts.
2. Authorised staff shall be able to register and maintain vehicles for workshop customers.
3. Vehicle records shall include relevant information such as registration number, make, model, model year, fuel type, and mileage where applicable.
4. A vehicle shall be associated with a customer.
5. Vehicle registration numbers shall follow a defined uniqueness and normalisation policy.
6. Customers shall be able to view only their own vehicles.
7. The system shall retain vehicle and service history when a vehicle is no longer actively serviced.

### FR-04: Appointment Booking and Management

1. Customers shall be able to submit appointment requests for their registered vehicles.
2. **Every appointment request shall require staff approval.** Submission shall not automatically create a confirmed booking.
3. Staff shall review requests and confirm or reject them based on available workshop slots.
4. Confirmed bookings shall not overlap in a way that exceeds the workshop's configured scheduling capacity.
5. Appointment records shall include the customer vehicle, requested or scheduled date and time, status, and relevant notes.
6. Customers shall be able to view the status of their own appointment requests.
7. Rescheduling and cancellation shall follow defined permissions and business rules.
8. The system shall prevent conflicting confirmed bookings when a slot is no longer available.
9. Appointment status changes shall be validated and recorded consistently.

Initial appointment statuses shall include `PENDING_APPROVAL`, `CONFIRMED`, `REJECTED`, and `CANCELLED`. Additional statuses may be introduced if required by the final workflow.

### FR-05: Service Jobs and Job Cards

1. Authorised staff shall be able to create a service job for a vehicle.
2. A service job may originate from a confirmed appointment or a **walk-in without an appointment**.
3. The appointment reference on a service job shall therefore be optional.
4. A job card shall capture the reported problem, inspection findings, assigned mechanic where applicable, tasks, parts used, and service status.
5. The system shall support tracking a job from creation through inspection, repair, completion, and delivery.
6. Authorised users shall be able to assign or reassign mechanics according to permissions.
7. A job shall not be marked delivered until the required service and billing checks have been completed.
8. Service history shall remain available for future reference.

### FR-06: Service Catalogue, Estimates, and Customer Approval

1. Authorised users shall be able to manage the service catalogue, including service descriptions, base prices, and estimated durations.
2. Staff shall be able to prepare an estimate containing itemised labour and parts.
3. Each estimate item shall retain the applicable price used when the estimate was prepared.
4. Estimate totals shall be calculated and validated by the backend.
5. Customers shall be able to review and approve or reject estimates for their own vehicles and service jobs.
6. The system shall record the estimate version, approval status, and relevant timestamps.
7. **Additional repair work shall require a revised estimate and customer approval before the additional work begins.**
8. Approval of an earlier estimate shall not automatically authorise newly added work or costs.
9. Changes to an estimate shall preserve the history needed to understand what was originally proposed and what the customer subsequently approved.

### FR-07: Employee and Mechanic Management

1. Authorised administrators shall be able to manage employee records and staff accounts.
2. Employee profiles shall contain relevant information such as name, contact details, job title, and specialisation where applicable.
3. The system shall support assigning mechanics to service jobs.
4. Staff permissions shall be based on assigned roles or permissions rather than frontend visibility alone.
5. Deactivated employees shall not be available for new assignments where the workflow prohibits it.
6. Historical job assignments shall remain traceable.

### FR-08: Invoices and Payment Recording

1. Authorised staff shall be able to create invoices for applicable service jobs.
2. Invoices shall contain itemised labour and parts charges.
3. The billing calculation shall support configurable tax rates and authorised discounts.
4. Tax and discount rules shall be applied consistently by the backend.
5. The system shall retain the relevant item prices, tax details, discounts, and totals used for each invoice.
6. The backend shall calculate invoice totals; clients shall not be trusted to supply authoritative totals.
7. Staff shall be able to record payments made to the workshop, including the amount, payment method, payment status, payment time, and reference where available.
8. **Partial payments and outstanding balances shall be supported.** An invoice may remain partially paid until the outstanding amount is settled.
9. The invoice status shall reflect its payment state, such as unpaid, partially paid, or paid. Additional statuses may be required for cancellation or other permitted cases.
10. Payment records shall be linked to the relevant invoice, and the system shall prevent invalid amounts or inconsistent payment totals.
11. Version 1 shall record workshop payments only. **Online payment processing and payment-gateway integration are out of scope.**
12. Financial records shall not be casually deleted or overwritten. Corrections shall follow controlled business rules that preserve the history of financial changes.

### FR-09: Spare-Parts Inventory

1. Authorised staff shall be able to manage spare-part records, including names, identifiers, prices, and stock quantities.
2. The system shall record parts consumed by service jobs.
3. Inventory quantities shall be updated consistently when parts are issued or returned.
4. The system shall prevent stock quantities from becoming invalid through concurrent or repeated operations.
5. The system shall support low-stock identification.
6. Stock updates associated with a service operation shall use appropriate transaction handling.
7. Historical job and invoice records shall retain the applicable part descriptions and prices even if catalogue details later change.

### FR-10: Dashboards and Reports

1. The application shall provide role-appropriate dashboards.
2. Workshop dashboards shall summarise operational information such as pending appointment requests, confirmed bookings, active jobs, completed jobs, and outstanding invoices.
3. Customer dashboards shall show the customer's own vehicles, appointments, service progress, estimates, and invoices.
4. Administrative reports shall provide relevant operational summaries, subject to permissions.
5. Report figures shall be derived from persisted business records and consistent calculation rules.

### FR-11: Notifications

1. Version 1 shall provide **in-app updates** for relevant events.
2. Notification events may include appointment decisions, estimate requests, revised estimates, service progress, and invoice or payment updates.
3. Customers shall receive only notifications they are authorised to see.
4. Notification records shall identify the intended recipient and relevant event.
5. Email, SMS, and external notification-provider integrations are deferred beyond Version 1.

### FR-12: Audit History and Administration

1. The system shall record important business events where auditability is needed.
2. Audit entries may include appointment status changes, estimate approvals, invoice changes, payment recording, stock adjustments, and administrative account changes.
3. Audit history shall identify the actor, event, timestamp, and relevant record.
4. Audit information shall be protected against unauthorised modification.
5. Access to audit records shall be restricted to authorised users.
6. Audit history shall not unnecessarily store passwords, authentication tokens, or other secrets.

## 6. Business Rules

The following rules apply across the application:

1. **Booking approval:** Every appointment requires staff approval. A request is not a confirmed appointment until approved and assigned an available slot.
2. **Scheduling conflicts:** The backend must validate slot availability when confirming or rescheduling appointments, not merely when the request is first submitted.
3. **Walk-in services:** A service job may exist without an appointment. The system must not create a fake appointment to represent a walk-in.
4. **Customer ownership:** Customers may access only records belonging to them, enforced by the backend.
5. **Estimate approval:** Work outside the currently approved estimate must not proceed until the customer approves the applicable revised estimate.
6. **Price history:** Changes to catalogue prices must not silently change previously approved estimates, issued invoices, or historical financial records.
7. **Financial calculations:** Monetary calculations and authoritative totals must be performed by the backend using appropriate decimal precision.
8. **Discount authorisation:** Discounts may be applied only by users or workflows authorised under the configured discount policy.
9. **Payment reconciliation:** The recorded payment total must be consistent with the invoice balance and payment status. Partial payments are permitted.
10. **Inventory integrity:** Stock changes must be validated and persisted consistently, including when multiple operations occur close together.
11. **Service completion:** A job must pass the required completion and billing checks before the vehicle is marked delivered.
12. **Record retention:** Important service and financial history must remain traceable. Deletion or correction must follow controlled rules.
13. **Single location:** Version 1 serves one workshop location. Multi-branch management is not required.
14. **Transactions:** Related database changes, such as recording a payment and updating an invoice balance, must be handled consistently using appropriate transaction boundaries.

## 7. Non-Functional Requirements

### 7.1 Security

- Passwords shall be securely hashed.
- Protected API endpoints shall enforce authentication and authorisation.
- Customer ownership checks shall be performed on the backend.
- Secrets and database credentials shall not be committed to version control.
- Input validation and safe error handling shall be applied.
- Sensitive customer, invoice, payment, and employee information shall be restricted to authorised users.

### 7.2 Reliability and Data Integrity

- Database relationships and constraints shall protect data integrity.
- Critical business operations shall use transactions where appropriate.
- Duplicate submissions and invalid state transitions shall be handled safely.
- Financial and service histories shall remain consistent.

### 7.3 Maintainability

- The backend shall follow a layered architecture: Controller → Service → Repository → Database.
- DTOs shall be used where appropriate to separate API contracts from persistence entities.
- Controllers shall focus on HTTP handling, services on business rules, and repositories on data access.
- The React frontend shall use reusable components and a clear separation between API integration and presentation.
- Naming, error responses, validation, and API conventions shall be consistent.

### 7.4 Usability and Responsiveness

- Interfaces shall be clear and appropriate for each role.
- The frontend shall support common desktop and mobile screen sizes.
- Forms shall provide validation feedback and meaningful success or failure messages.
- Loading, empty, and error states shall be handled explicitly.

### 7.5 Testability

- Backend business rules shall be testable independently of HTTP controllers where practical.
- API endpoints shall be testable using Postman and automated tests.
- Important workflows shall include tests for valid operations, invalid transitions, access control, and edge cases.
- Database relationships and financial calculations shall receive particular attention.

### 7.6 Documentation and Version Control

- Requirements, database design, architecture, API behaviour, and development milestones shall be documented.
- Git commits shall be small and meaningful.
- Credentials and other secrets shall not be committed.
- Documentation shall be updated as agreed requirements and implementation evolve.

## 8. Version 1 Scope

### Included

- Authentication and role-based access control.
- Customer self-registration and staff-created customer records.
- Customer and vehicle management.
- Staff-approved appointment booking and scheduling conflict checks.
- Walk-in service registration.
- Service jobs, job cards, and mechanic assignment.
- Service catalogue, estimates, and revised-estimate approval.
- Itemised invoices, configurable taxes, authorised discounts, and workshop payment recording.
- Partial payments and outstanding balances.
- Basic spare-parts inventory and stock consumption.
- Role-specific dashboards and basic reports.
- In-app notifications.
- Appropriate validation, exception handling, tests, and documentation.

### Deferred Beyond Version 1

- Online payment gateway integration.
- Email and SMS notification-provider integrations.
- Multi-branch or multi-workshop management.
- Advanced accounting and external accounting integrations.
- Advanced business intelligence and analytics.
- Dedicated mobile applications.
- Other integrations or features that are not necessary to demonstrate the core end-to-end workflow.

## 9. Acceptance Criteria

Version 1 will be considered functionally complete when the following outcomes can be demonstrated:

1. A customer can register, sign in, and manage their own vehicle records.
2. A customer can submit an appointment request and see its pending status.
3. Staff can confirm a request only when the required slot is available, or reject it with an appropriate outcome.
4. The system prevents conflicting confirmed bookings under the configured scheduling rules.
5. Staff can register a walk-in service and create a service job without an appointment.
6. Staff can prepare an estimate, and the customer can approve or reject it.
7. Additional work cannot proceed until the applicable revised estimate has been approved.
8. An authorised user can create an invoice with itemised labour, parts, applicable taxes, and authorised discounts.
9. Staff can record full or partial workshop payments, and the invoice reflects the remaining balance correctly.
10. Stock movements associated with service jobs are validated and persisted consistently.
11. Customers cannot retrieve other customers' private records by changing API identifiers or request parameters.
12. Role restrictions are enforced by the backend.
13. Relevant in-app updates are available to authorised recipients.
14. Critical workflows have appropriate automated or documented API tests.
15. The application can be started and tested using the documented development setup.

## 10. Assumptions and Constraints

- Version 1 represents a single workshop location.
- Customers and workshop staff use the same overall application but receive role-appropriate interfaces and permissions.
- The application is developed as a portfolio project, with maintainability and demonstrable end-to-end functionality as priorities.
- Workshop operating hours, appointment-slot duration, closure dates, and any applicable scheduling capacity must be configured or defined before scheduling is implemented.
- Tax settings and discount authorisation rules must be defined sufficiently for the workshop's intended billing workflow before invoicing is implemented.
- Payments are recorded by workshop staff; the system does not process online transactions in Version 1.
- All final database relationships, constraints, and status transitions must be reviewed against the approved requirements before implementation.

## 11. Requirements Change Policy

Changes to requirements shall be reviewed before implementation. A change that affects roles, business rules, database relationships, financial calculations, security, or existing records must be evaluated for its impact on the database design, API contracts, tests, and documentation.

The requirements document, database design, and implementation should remain aligned as the project evolves.

---

**Review status:** Ready for review before saving to the repository.

**Next document:** `docs/database-design.md`, to be prepared after this requirements draft is reviewed.
