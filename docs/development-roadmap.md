# VSMS Development Roadmap

Project Vehicle Service Management System
Version 1.0
Last updated 29 October 2026
Status Documentation and planning phase

## 1. Project Overview

VSMS is a full-stack vehicle service centre management application with separate interfaces for customers and workshop staff.

Technology stack
- Frontend React
- Backend Java 21, Spring Boot, REST API
- Database MySQL 8, Spring Data JPA, Hibernate
- API testing Postman
- Backend IDE Eclipse
- Frontend IDE VS Code
- Version control Git and GitHub

Repository httpsgithub.comMohith-M-2004vehicle-service-management-system

Local project `DPROJECTSVehicle-Service-Management-System`

## 2. Current Project Status

 Item  Status
------
 Git repository and initial commit  Completed
 Spring Boot backend scaffold  Completed
 MySQL database created  Completed
 Backend-to-MySQL connection verified  Completed
 Home API endpoint verified  Completed
 Requirements document  Committed and pushed
 Database design document  Committed and pushed
 Development roadmap  In progress
 Business entities and tables  Not started
 Authentication and authorization  Not started
 React frontend  Not started

Latest confirmed Git push `0e69262` on `main`.

## 3. Development Milestones

### Phase 1 Project Foundation and Planning

- [x] M1 Repository and project structure
  - Create GitHub repository.
  - Establish `backend`, `frontend`, `database`, and `docs`.
  - Commit and push the initial project.

- [x] M2 Backend and database setup
  - Generate Spring Boot project.
  - Configure Java 21 and Maven.
  - Create MySQL database `vsms_db`.
  - Configure environment-based database credentials.
  - Verify successful application startup and database connection.
  - Verify `GET ` returns `VSMS API is running!`.

- [x] M3 Requirements specification
  - Define user roles and business modules.
  - Establish booking, estimate, billing, payment, and inventory rules.
  - Define Version 1 scope and acceptance criteria.
  - Save `docsrequirements.md` and push it to GitHub.

- [x] M4 Database design
  - Identify core entities and relationships.
  - Separate users, customer profiles, and employee profiles.
  - Separate appointments, service jobs, estimates, invoices, and payments.
  - Account for walk-ins, partial payments, revised estimates, and stock transactions.
  - Save `docsdatabase-design.md` and push it to GitHub.

- [ ] M5 Development roadmap
  - Maintain this roadmap as `docsdevelopment-roadmap.md`.
  - Confirm milestones, dependencies, and completion criteria.
  - Commit and push the document.

### Phase 2 Pre-Implementation Review

- [ ] M6 Review existing project configuration
  - Inspect backend package structure and Maven dependencies.
  - Review database connection and JPA configuration.
  - Review Git status and ignore rules.
  - Decide on a controlled database migration approach.
  - Identify any configuration changes needed before implementation.

Exit criteria The existing project is understood, secrets are protected, and the initial implementation approach is agreed upon.

### Phase 3 Backend Foundations and Security

- [ ] M7 Backend architecture foundations
  - Establish Controller → Service → Repository layering.
  - Define DTO conventions and validation patterns.
  - Set up consistent exception handling and API response behaviour.
  - Establish testing conventions.

- [ ] M8 Authentication and authorization
  - Implement secure password hashing.
  - Implement login and customer registration.
  - Define Administrator, Workshop Staff, and Customer roles.
  - Enforce authorization on the backend.
  - Verify customer resource ownership.
  - Test unauthorized and forbidden requests.

Exit criteria Users can authenticate securely, and backend permissions are enforced.

### Phase 4 Customer and Vehicle Management

- [ ] M9 Customer management
  - Customer self-registration.
  - Staff-created customer records.
  - Customer profile viewing and updating.
  - Validation and duplicate handling.

- [ ] M10 Vehicle management
  - Register vehicles against customers.
  - View and update vehicle information.
  - Enforce registration-number rules.
  - Ensure customers can access only their own vehicles.

Exit criteria Customer and vehicle APIs pass positive, validation, and authorization tests.

### Phase 5 Appointments and Service Jobs

- [ ] M11 Appointment management
  - Submit booking requests.
  - Staff approval, rejection, and rescheduling.
  - Check slot availability and booking conflicts.
  - Customer cancellation rules.
  - Configure operating hours, slot duration, and closure dates.

- [ ] M12 Service jobs and job cards
  - Create jobs from confirmed appointments.
  - Support walk-in jobs without appointments.
  - Assign employees or mechanics.
  - Record reported problems and inspection notes.
  - Enforce valid job-status transitions.

Exit criteria A booking can progress into a service job, and walk-in jobs work independently.

### Phase 6 Estimates, Services, and Inventory

- [ ] M13 Service catalogue and estimates
  - Manage available service types.
  - Create itemised labour and parts estimates.
  - Track estimate versions.
  - Record customer approvals or rejections.
  - Require approval of revised estimates before additional work.

- [ ] M14 Spare-parts inventory
  - Manage parts and stock levels.
  - Record parts used in jobs.
  - Validate stock availability.
  - Update inventory transactionally.
  - Add low-stock indicators and adjustment rules.

Exit criteria Approved work is traceable, historical prices are preserved, and stock remains consistent.

### Phase 7 Invoices and Payments

- [ ] M15 Invoice management
  - Generate itemised invoices.
  - Calculate configured taxes and authorised discounts.
  - Preserve historical invoice values.
  - Define invoice finalisation, correction, and cancellation rules.

- [ ] M16 Payment recording
  - Allow workshop staff to record payments.
  - Support partial payments and outstanding balances.
  - Track payment methods and references.
  - Validate payment amounts and status.
  - Reconcile invoice balances against recorded payments.

Exit criteria Completed jobs can be billed accurately, with outstanding balances correctly calculated.

### Phase 8 React Frontend

- [ ] M17 React application foundation
  - Set up the frontend in `frontend`.
  - Configure routing and API communication.
  - Establish reusable components and responsive layouts.
  - Implement loading, empty, success, and error states.

- [ ] M18 Customer portal
  - Registration and login.
  - Customer profile and vehicle management.
  - Appointment requests and booking history.
  - Estimate review and approval.
  - Invoice and payment-history viewing.
  - In-app notifications.

- [ ] M19 Workshop staff interface
  - Role-appropriate dashboards.
  - Customer and vehicle management.
  - Appointment approval and scheduling.
  - Job cards and employee assignments.
  - Estimates, inventory, invoices, and payment recording.

- [ ] M20 Administrator interface
  - User and employee administration.
  - Service catalogue management.
  - Configuration and access management.
  - Audit history and basic operational reports.

Exit criteria The React interfaces complete the supported workflows through the REST API, with permissions enforced by the backend.

### Phase 9 Testing, Security, and Quality

- [ ] M21 Backend and API testing
  - Unit tests for business rules.
  - Integration tests for persistence and transactions.
  - Postman tests for successful and failed requests.
  - Validation, authorization, ownership, and status-transition tests.

- [ ] M22 End-to-end workflow testing
  - Customer registration → vehicle registration → appointment request.
  - Staff approval → job creation → inspection.
  - Estimate creation → customer approval → service completion.
  - Invoice generation → partial payments → settled balance.
  - Inventory consumption and low-stock checks.

- [ ] M23 Security and reliability review
  - Protect credentials and configuration secrets.
  - Review role permissions and customer data isolation.
  - Verify transaction boundaries and financial calculations.
  - Review error responses, logging, and audit records.
  - Check frontend handling of expired sessions and failed requests.

Exit criteria Critical workflows pass repeatable tests, and known defects are documented and addressed.

### Phase 10 Documentation and Portfolio Release

- [ ] M24 Documentation
  - Update README with setup instructions.
  - Document environment variables and local configuration.
  - Document API endpoints and examples.
  - Maintain database design and architecture documentation.
  - Include testing instructions and screenshots where useful.

- [ ] M25 Portfolio-ready release
  - Review Git history and repository cleanliness.
  - Verify a fresh local setup using documented instructions.
  - Prepare a demonstration workflow.
  - Publish a stable release when the Version 1 acceptance criteria are satisfied.

Exit criteria Another developer can understand, configure, run, and demonstrate the project using the repository documentation.

## 4. Implementation Rules

1. Complete one meaningful milestone at a time.
2. Verify each milestone before marking it complete.
3. Build the backend and test its APIs before integrating each frontend workflow.
4. Keep controllers, services, repositories, DTOs, and persistence entities appropriately separated.
5. Never commit credentials, tokens, or passwords.
6. Use controlled database changes and avoid destructive operations without explicit review.
7. Preserve financial and audit history.
8. Make small, meaningful Git commits and push completed work regularly.
9. Record important decisions and changes in the project documents.
10. Do not mark work as completed merely because code has been written. Verify that it works.

## 5. Deferred Enhancements

Consider these after the core Version 1 workflow is stable

- External payment gateway integration.
- Email and SMS notifications.
- Multi-branch workshop management.
- Advanced analytics and reporting.
- Supplier purchasing and advanced stock accounting.
- Mobile application.
- Advanced accounting integrations.

## 6. Current Next Actions

1. Save this document as `docsdevelopment-roadmap.md`.
2. Run `git status` and review the staged diff before committing.
3. Commit and push the roadmap.
4. Review the existing backend configuration and project structure.
5. Agree on any necessary configuration or migration changes before implementing the first business module.

Current focus Complete M5, then proceed to M6. No business entities or React pages have been implemented yet.

Progress tracking policy Update this roadmap whenever a milestone is verified, a requirement changes, or a new implementation decision affects the project sequence.
