**

Comprehensive Design Document: Integrated Business Operations Platform

Version: 1.0

Date: July 19, 2025

This document outlines the architecture and functional design for a unified Integrated Business Operations Platform. This platform combines Asset Inventory Management, Booking System capabilities, and Email Campaign Management into a cohesive microservices-based application. The system is designed to provide robust tools for internal administrative teams, enabling efficient tracking of physical and digital assets, streamlined booking processes, and targeted communication campaigns.
# System Requirements
This section details the functional and non-functional requirements that drive the design of the integrated platform.
1. Functional Requirements
**1.1.1. Asset Inventory Management (AIM)**
* **Comprehensive Asset Management:**

    * Administrators must be able to perform full CRUD (Create, Read, Update, Delete) operations for every asset.

    * The system must support unique asset identification, using serial numbers or custom-generated IDs (with QR code/barcode support).

    * Assets must be classifiable into user-defined categories (e.g., "IT Equipment," "Furniture," "Vehicles").

* **Lifecycle Tracking:**

    * The system must track the status of each asset throughout its lifecycle (e.g., 'In Stock', 'Assigned', 'Under Maintenance', 'Disposed').

    * Assets must be assignable to specific users or locations (e.g., 'John Doe', 'Head Office - Floor 2').

* **Audit & History Trail:**

    * Every action performed on an asset (creation, update, status change, assignment) must be logged in an immutable audit trail.

    * The history for any asset must be viewable, showing who did what, and when.

* **Search & Reporting:**

    * Admins must have powerful search and filtering capabilities to find assets based on any attribute (status, category, user, location, purchase date).

    * The system must generate summary reports (e.g., total assets by category, asset depreciation, maintenance schedules).

* **Notifications & Alerts:**

    * The system should generate alerts for key events, such as low stock levels for a specific asset category, or upcoming warranty expirations and scheduled maintenance.

**1.1.2. Booking System (BS)**
* **Resource Management:**

    * Admins must be able to define bookable resources (e.g., meeting rooms, equipment, vehicles).

    * Each resource should have properties like name, description, capacity, and availability rules.

* **Booking Management:**
    * Users must be able to view resource availability.
    * Users must be able to create, modify, and cancel bookings for available resources.
    * Bookings must include details like start time, end time, booking user, and purpose.
* **Conflict Prevention:**
    * The system must prevent double-bookings for resources.
* **Notifications:**
    * Users should receive confirmation notifications for successful bookings, modifications, and cancellations.
    * Admins should be notified of new bookings or conflicts.

**1.1.3. Email Campaign Management (ECM)**
* **Campaign Scheduling:**
    * Ability to schedule email campaigns to run at specified times or triggered by external events.
* **Recipient Management:**
    * Define recipient lists, apply filters (e.g., unsubscribe status, send limits).
* **Email Sending:**
    * Send personalized emails based on templates with variable substitution.
    * Embed tracking pixels for open rates.
    * Log all send events.
* **Rate Limiting:**
    * Enforce email sending limits per hour/day.
    * Implement random delays to avoid spam detection.
* **Template Management:**
    * Store and manage email templates.
    * Support per-user variables and default values.
* **Unsubscribe Handling:**
    * Process unsubscribe requests and update recipient status.
* **Engagement Tracking:**
    * Track email open events (via pixel).
    * Monitor IMAP for replies.
* **Reply Analysis:**
    * Analyze raw reply text for sentiment/intent.
* **Reporting & Analytics:**
    * Aggregate and summarize campaign logs.
    * Generate reports on campaign performance (e.g., open rates, click-through rates, reply rates).
* **Error Notification:**
    * Alert administrators of campaign-related errors (e.g., failed sends, API issues).
**1.1.4. Platform-wide Requirements**
* **User & Access Control:**
    * The system must have a secure login mechanism for administrators.
    * Support for different roles (e.g., 'Super Admin', 'Asset Admin', 'Booking Manager', 'Campaign Manager', 'Viewer').
* **Unified Dashboard:**
    * A single, intuitive administrative dashboard to manage all functionalities.
* **Integration Points:**
    * Potential for Asset Inventory to integrate with Booking System (e.g., booking an 'IT Equipment' asset).
    * Potential for Email Campaigns to utilize user data from a central user store or directly from booking participants.
1.2. Non-Functional Requirements
* **Data Integrity:** The system must ensure the accuracy and consistency of all data across services at all times. This is the highest priority.
* **Usability & Simplicity:** The user interface must be clean, intuitive, and highly efficient for administrators performing daily tasks across all modules.
* **High Reliability:** The system must be consistently available during business hours. Each microservice should be designed for resilience.
* **Security:** Access must be strictly limited to authenticated and authorized administrators. Data must be protected in transit and at rest.
* **Auditability:** The system must provide a clear and unalterable record of all activities for compliance and accountability within each relevant service.
* **Scalability:** While initially for a small user base, the microservices architecture should allow individual services to scale independently as needed.
* **Maintainability:** The codebase for each service should be modular, well-documented, and easy to understand and modify.
* **Performance:** API response times should be fast and consistent, providing a smooth user experience. 
# High-Level System Design
### 2.1. Client Applications (Presentation Tier):

To cater to the distinct user groups, we will implement three separate Single-Page Applications (SPAs). Each SPA will consume APIs from the API Gateway, ensuring appropriate access control and routing.
- **2.1.1. Admin Dashboard (Internal Platform Administrators)**
    
    - **Purpose:** Comprehensive control and oversight of the entire platform. This is for the team managing the software itself.
        
    - **Functionality:** Full CRUD for assets, managing platform users (including customers), monitoring system health, viewing global reports, troubleshooting.
        
    - **Technology Stack:** Modern SPA framework (e.g., React, Vue.js, Angular) for a rich, dynamic, and responsive interface.
        
    - **Authentication:** Requires strong authentication, primarily managed by the User Management Service.
        
- **2.1.2. Customer Portal (Software Purchasers)**
    
    - **Purpose:** Allows customers to configure and manage their specific instance of the platform's offerings. Multi-tenancy is a key consideration here.
        
    - **Functionality:**
        
        - **Asset Management (for their assets):** Define their own assets, track their lifecycle, manage inventory for items they wish to offer (e.g., equipment they rent out).
            
        - **Booking System Configuration:** Define their bookable resources, set availability, view their bookings, manage booking notifications.
            
        - **Email Campaign Management:** Create and schedule campaigns for their audience, manage templates, view campaign analytics.
            
        - **User Management (for their clients):** Manage access for clients to their specific offerings.
            
    - **Technology Stack:** Modern SPA framework (e.g., React, Vue.js, Angular) for a robust and configurable experience.
        
    - **Authentication:** Managed by the User Management Service, with customer-specific roles and permissions.
        
- **2.1.3. Client Facing Portal (End-Users of Customer Offerings)**
    
    - **Purpose:** Allows end-users to interact with offerings provided by a specific customer. This portal might be whitelabelled or branded for each customer.
        
    - **Functionality:**
        
        - **Booking System Interaction:** View availability for specific resources offered by a customer, make new bookings, view/manage their own existing bookings (limited to their own bookings).
            
        - **Minimal Asset View:** Potentially view details of assets they have booked (e.g., specs of a rented vehicle).
            
        - **Unsubscribe Link:** Functionality to unsubscribe from email campaigns.
            
    - **Technology Stack:** Lightweight and performant SPA framework (e.g., Preact, Svelte, or even plain JavaScript if simple enough) for fast loading and responsiveness, optimized for public access.
        
    - **Authentication:** Could be optional (for public bookings) or light (e.g., magic links, simple email/password for repeat users), managed by the User Management Service, potentially with a separate client registration flow.
        

### 2.2. Backend Microservices (Application Tier):

Each microservice will encapsulate its own business logic, manage its own persistence, and communicate with other services via well-defined APIs or message queues.

- **2.2.1. API Gateway:**
    
    - **Role:** The single entry point for all client requests from the three SPAs.
        
    - **Responsibilities:** Request routing to appropriate microservices, authentication enforcement (validating tokens), authorization checks (consulting UMS), rate limiting, potentially request aggregation.
        
    - **Technology:** Nginx, Envoy, or a dedicated API Gateway solution like Kong or AWS API Gateway.
        
- **2.2.2. User Management Service (UMS):**
    
    - **Role:** Centralized identity and access management for all user types (Platform Admins, Customers, Clients).
        
    - **Responsibilities:** User registration, login, password management, token issuance (e.g., JWTs), role-based access control (RBAC), multi-tenancy management for customers.
        
    - **Key Consideration:** How to differentiate and manage permissions for platform admins, customer organizations, and individual clients within those organizations. This service will likely have `organizations` or `tenants` concept.
        
- **2.2.3. Asset Inventory Service (AIS):**
    
    - **Role:** Manages the lifecycle and attributes of all physical and digital assets.
        
    - **Responsibilities:** Asset CRUD, category management, location tracking, supplier information, audit logging for asset changes, reporting on asset data.
        
    - **Key Consideration:** Multi-tenancy. Assets will belong to specific "Customers" (organizations). All queries and operations must be scoped to the authenticated customer's assets.
        
- **2.2.4. Booking Service (BKS):**
    
    - **Role:** Manages bookable resources and the booking process.
        
    - **Responsibilities:** Resource definition (name, type, capacity, availability rules), booking creation, modification, cancellation, conflict detection, availability querying.
        
    - **Key Consideration:** Multi-tenancy. Resources and bookings will belong to specific "Customers." Clients will only see/book resources exposed by the customer they are interacting with.
        
- **2.2.5. Email Campaign Service (ECS):**
    
    - **Role:** Handles the creation, scheduling, sending, and tracking of email campaigns.
        
    - **Responsibilities:** Campaign management, template storage, recipient list management, email sending (integrating with ESPs), open/reply tracking, rate limiting, campaign analytics.
        
    - **Key Consideration:** Multi-tenancy. Campaigns, templates, and recipient lists are owned by "Customers." Sending limits and analytics are scoped per customer.
        
- **2.2.6. Notification Service (New - Separated from other services):**
    
    - **Role:** A dedicated service for handling all platform-generated notifications across various channels.
        
    - **Responsibilities:** Receiving notification requests (events) from other services, determining appropriate recipients, formatting messages, and dispatching via various channels (email, in-app, potentially SMS, Slack).
        
    - **Integration:** Subscribes to events from AIS (e.g., `low_stock_alert`), BKS (e.g., `booking_created`), ECS (e.g., `campaign_error`). Makes calls to ECS for email notifications, or internal APIs for in-app.
        

### 2.3. Common Components:

- **Service Discovery:** Allows microservices to find and communicate with each other dynamically (e.g., Eureka, Consul, Kubernetes DNS).
    
- **Message Broker (e.g., Kafka, RabbitMQ):** Facilitates asynchronous, event-driven communication between services. Essential for decoupling and enabling scalability. Used for audit trails, notifications, and cross-service data synchronization.
    
- **Centralized Logging & Monitoring:** Tools like ELK stack (Elasticsearch, Logstash, Kibana) or Splunk for aggregated logs; Prometheus and Grafana for metrics and dashboards across all services and client applications.
    
- **Distributed Tracing:** Tools like Jaeger or Zipkin to trace requests across multiple microservices, crucial for debugging in a distributed environment.
    
- **Secrets Management:** Secure storage for API keys, database credentials (e.g., Vault, AWS Secrets Manager).
  
### 2.4. Deployment:

- **Containerization:** All microservices and client applications will be containerized using Docker.
    
- **Orchestration:** Kubernetes will be used for automated deployment, scaling, load balancing, and management of containers across a cloud infrastructure (e.g., AWS EKS, Google GKE, Azure AKS).
    
- **CI/CD Pipelines:** Automated pipelines (e.g., Jenkins, GitLab CI/CD, GitHub Actions) for continuous integration and continuous deployment of individual microservices and client applications. Each service and client app can have its own pipeline.
## Detailed Database Design
Each microservice will manage its own data store. While PostgreSQL is suggested as a primary choice for relational data, specific needs might warrant other database types.
**3.1. User Management Service (UMS) Database Schema:**
* **users:** Manages platform administrator accounts.
    * `user_id` (PK, UUID)
    * `full_name` (VARCHAR)
    * `email` (VARCHAR, UNIQUE)
    * `password_hash` (VARCHAR)
    * `role` (ENUM('super_admin', 'asset_admin', 'booking_manager', 'campaign_manager', 'viewer'))
    * `created_at` (TIMESTAMP)
    * `updated_at` (TIMESTAMP)
* **user_sessions:** Stores active user sessions (e.g., JWT blacklisting or opaque tokens).
    * `session_id` (PK, UUID)
    * `user_id` (FK to users)
    * `token` (TEXT)
    * `expires_at` (TIMESTAMP)
    * `created_at` (TIMESTAMP)
**3.2. Asset Inventory Service (AIS) Database Schema:**
* **asset_categories:** For organizing assets.
    * `category_id` (PK, UUID)
    * `name` (VARCHAR, UNIQUE)
    * `description` (TEXT)
* **locations:** Physical or logical places where assets are held.
    * `location_id` (PK, UUID)
    * `name` (VARCHAR, UNIQUE)
    * `description` (TEXT)
* **suppliers:** Information on vendors.
    * `supplier_id` (PK, UUID)
    * `name` (VARCHAR)
    * `contact_info` (JSONB)
* **assets:** The central table of the Asset Inventory system.
    * `asset_id` (PK, UUID)
    * `name` (VARCHAR)
    * `description` (TEXT)
    * `category_id` (FK to asset_categories)
    * `status` (ENUM('in_stock', 'assigned', 'in_maintenance', 'disposed'))
    * `current_location_id` (FK to locations, NULLABLE)
    * `assigned_to_user_id` (UUID, conceptually linked to UMS user_id, but stored here)
    * `supplier_id` (FK to suppliers, NULLABLE)
    * `serial_number` (VARCHAR, UNIQUE, NULLABLE)
    * `custom_asset_id` (VARCHAR, UNIQUE, NULLABLE, for QR/barcode)
    * `purchase_date` (DATE)
    * `purchase_cost` (DECIMAL)
    * `warranty_expiry_date` (DATE, NULLABLE)
    * `custom_fields` (JSONB)
    * `created_at` (TIMESTAMP)
    * `updated_at` (TIMESTAMP)
* **asset_audit_log:** The immutable history for every asset.
    * `log_id` (PK, BIGSERIAL)
    * `asset_id` (FK to assets)
    * `user_id` (UUID, referencing UMS user_id)
    * `action` (VARCHAR, e.g., 'CREATED', 'UPDATED_STATUS', 'ASSIGNED_TO_USER', 'UPDATED_FIELD')
    * `change_details` (JSONB, storing before/after values of changed attributes)
    * `timestamp` (TIMESTAMP, DEFAULT NOW())
**3.3. Booking Service (BKS) Database Schema:**
* **resources:** Defines bookable items.
    * `resource_id` (PK, UUID)
    * `name` (VARCHAR)
    * `description` (TEXT)
    * `type` (ENUM('meeting_room', 'equipment', 'vehicle', 'other'))
    * `capacity` (INT, NULLABLE)
    * `is_available` (BOOLEAN, DEFAULT TRUE)
    * `location_id` (FK to AIS locations, NULLABLE, for cross-service linking)
    * `associated_asset_id` (UUID, conceptually linked to AIS asset_id, NULLABLE)
    * `created_at` (TIMESTAMP)
    * `updated_at` (TIMESTAMP)
* **bookings:** Stores individual booking instances.
    * `booking_id` (PK, UUID)
    * `resource_id` (FK to resources)
    * `booked_by_user_id` (UUID, referencing UMS user_id)
    * `start_time` (TIMESTAMP)
    * `end_time` (TIMESTAMP)
    * `purpose` (TEXT, NULLABLE)
    * `status` (ENUM('confirmed', 'pending', 'cancelled', 'completed'))
    * `created_at` (TIMESTAMP)
    * `updated_at` (TIMESTAMP)
* **resource_availability_rules:** (Optional, for complex recurring availability)
    * `rule_id` (PK, UUID)
    * `resource_id` (FK to resources)
    * `day_of_week` (INT, 1-7)
    * `start_time_of_day` (TIME)
    * `end_time_of_day` (TIME)
    * `is_recurring` (BOOLEAN)
    * `effective_from_date` (DATE)
    * `effective_to_date` (DATE, NULLABLE)
**3.4. Email Campaign Service (ECS) Database Schema:**
* **campaigns:** Defines email campaigns.
    * `campaign_id` (PK, UUID)
    * `name` (VARCHAR)
    * `description` (TEXT)
    * `status` (ENUM('draft', 'scheduled', 'sending', 'completed', 'cancelled'))
    * `scheduled_at` (TIMESTAMP, NULLABLE)
    * `sent_by_user_id` (UUID, referencing UMS user_id)
    * `template_id` (FK to email_templates)
    * `subject` (VARCHAR)
    * `from_email` (VARCHAR)
    * `send_rate_limit_per_hour` (INT, NULLABLE)
    * `created_at` (TIMESTAMP)
    * `updated_at` (TIMESTAMP)
* **email_templates:** Stores reusable email templates.
    * `template_id` (PK, UUID)
    * `name` (VARCHAR, UNIQUE)
    * `content_html` (TEXT)
    * `content_text` (TEXT)
    * `variables_schema` (JSONB, defines expected variables)
    * `created_at` (TIMESTAMP)
    * `updated_at` (TIMESTAMP)
* **recipients:** Stores email recipients.
    * `recipient_id` (PK, UUID)
    * `email` (VARCHAR, UNIQUE)
    * `first_name` (VARCHAR, NULLABLE)
    * `last_name` (VARCHAR, NULLABLE)
    * `is_unsubscribed` (BOOLEAN, DEFAULT FALSE)
    * `source` (VARCHAR, e.g., 'manual', 'booking_participants')
    * `custom_data` (JSONB)
    * `created_at` (TIMESTAMP)
    * `updated_at` (TIMESTAMP)
* **campaign_recipients:** Links recipients to campaigns (many-to-many).
    * `campaign_id` (FK to campaigns)
    * `recipient_id` (FK to recipients)
    * `status` (ENUM('pending', 'sent', 'failed'))
    * `send_attempted_at` (TIMESTAMP, NULLABLE)
    * `PRIMARY KEY (campaign_id, recipient_id)`
* **email_sends:** Logs every individual email send attempt.
    * `send_id` (PK, UUID)
    * `campaign_id` (FK to campaigns)
    * `recipient_id` (FK to recipients)
    * `sent_at` (TIMESTAMP)
    * `status` (ENUM('success', 'failed', 'throttled'))
    * `error_message` (TEXT, NULLABLE)
* **email_events:** Logs engagement events (opens, replies).
    * `event_id` (PK, UUID)
    * `send_id` (FK to email_sends)
    * `event_type` (ENUM('open', 'reply'))
    * `timestamp` (TIMESTAMP)
    * `ip_address` (VARCHAR, NULLABLE, for open events)
    * `user_agent` (TEXT, NULLABLE, for open events)
    * `raw_reply_text` (TEXT, NULLABLE, for reply events)
    * `reply_analysis` (JSONB, NULLABLE, for reply events, e.g., sentiment, intent)
* **rate_limiter_state:** (Redis or dedicated table for persistence)
    * `key` (VARCHAR, e.g., 'campaign:hourly_sent:campaign_id', 'daily_sent:campaign_id')
    * `count` (INT)
    * `reset_at` (TIMESTAMP)
4. Core Application Modules (Logical Breakdown within Microservices)
Each microservice will encapsulate its own business logic.

**4.1. User Management Service (UMS) Modules:**

* **Authentication Module:** Handles user registration, login, password hashing/verification (e.g., bcrypt), JWT generation, and session management.

* **Authorization Module:** Manages user roles and permissions. Provides middleware or decorators for API endpoint protection.

* **User Management Module:** CRUD operations for user accounts, role assignment.

**4.2. Asset Inventory Service (AIS) Modules:**

* **Asset Management Module:**

    * Responsibilities: Contains all business logic for asset CRUD operations.

    * Enforces validation rules (e.g., unique serial numbers).

    * Triggers the creation of audit log entries for every change via internal calls to the Audit & Logging Module.

* **Audit & Logging Module:**

    * Responsibilities: Provides an internal API for other modules within AIS to call whenever a tracked action occurs.

    * It is solely responsible for writing new entries to the `asset_audit_log` table.

* **Search & Reporting Module:**

    * Responsibilities: Contains the logic for building complex database queries based on user-supplied filters.

    * Includes functions to aggregate data and generate summary reports (e.g., total assets by category, depreciation calculations).

* **Taxonomy Module:**

    * Responsibilities: Manages the CRUD operations for supporting data like `asset_categories`, `locations`, and `suppliers`.

* **Alerts Module:**

    * Responsibilities: Monitors asset data for predefined conditions (e.g., `warranty_expiry_date` approaching, `status` changes to 'disposed').

    * Publishes alert events to the Message Broker for notification processing.

**4.3. Booking Service (BKS) Modules:**

* **Resource Management Module:**

    * Responsibilities: CRUD operations for `resources`. Defines resource properties and availability.

* **Booking Management Module:**

    * Responsibilities: Handles booking creation, modification, and cancellation.

    * Implements conflict detection logic to prevent double-bookings.

    * Validates booking times against resource availability rules.

    * Publishes booking events (e.g., `booking_created`, `booking_updated`, `booking_cancelled`) to the Message Broker.

* **Availability Checker Module:**

    * Responsibilities: Provides an efficient way to query resource availability for a given time range.

**4.4. Email Campaign Service (ECS) Modules:**

* **Campaign Scheduler Module:**

    * Responsibilities: Triggered by cron jobs or internal events.

    * Reads campaign configurations from the database.

    * Enqueues campaign runs (e.g., to a job queue or message broker for the Campaign Executor).

* **Campaign Executor Module:**

    * Responsibilities: Accepts `campaign_id` from the scheduler.

    * Loads recipients based on campaign configuration.

    * Applies filters (unsubscribe status, send limits via Rate Limiter).

    * Calls the "Send Email" module for each eligible recipient.

* **Send Email Module:**

    * Responsibilities: Receives recipient data + `template_id`.

    * Loads the correct `email_template` from storage (Template Resolver).

    * Applies variable substitution.

    * Embeds tracking pixel URL.

    * Interacts with an external Email Service Provider (ESP) API (e.g., SendGrid, Mailgun) to send the email.

    * Logs send events to the `email_sends` table.

* **Rate Limiter Module:**

    * Responsibilities: Checks and enforces current send rates (emails/hour/day) for campaigns.

    * Uses external state (e.g., Redis or `rate_limiter_state` table) to track counts.

    * Applies random delays to smooth out sending.

* **Template Resolver Module:**

    * Responsibilities: Loads the correct `email_template` by `template_id`.

    * Applies per-user variables or defaults from campaign configuration.

* **Unsubscribe Handler Module:**

    * Responsibilities: Processes unsubscribe requests (e.g., via webhook from ESP or direct API calls).

    * Updates `is_unsubscribed` status in the `recipients` table.

* **Engagement Tracker (Pixel Logger) Module:**

    * Responsibilities: Exposes a public endpoint for tracking pixels.

    * Logs "open" events to the `email_events` table.

* **IMAP Monitor Module:**

    * Responsibilities: Periodically checks a configured inbox for replies.

    * Parses reply threads to identify original campaigns/recipients.

    * Publishes "reply" events to the Message Broker for the Reply Analyzer.

* **Reply Analyzer Module:**

    * Responsibilities: Accepts raw reply text.

    * Performs sentiment/intent classification using NLP libraries or external services.

    * Outputs a label and confidence score, updates `email_events`.

* **Analytics Aggregator Module:**

    * Responsibilities: Periodically summarizes data from `email_sends` and `email_events` tables.

    * Writes aggregated reports to a summary table or publishes for external reporting tools.

* **Error Notifier Module:**

    * Responsibilities: Accepts error events + metadata from other ECS modules.

    * Sends alerts to configured channels (e.g., Telegram, email, Slack).

# API Endpoints (REST API)
All external API requests will pass through the API Gateway, which will route them to the appropriate microservice. Internal service-to-service communication can use direct HTTP calls or asynchronous messaging.

**5.1. API Gateway Endpoints:**

The API Gateway will expose a unified set of endpoints, mapping them to the underlying microservices.

* `/api/auth/*` -> User Management Service

* `/api/assets/*` -> Asset Inventory Service

* `/api/bookings/*` -> Booking Service

* `/api/campaigns/*` -> Email Campaign Service

* `/api/templates/*` -> Email Campaign Service

* `/api/recipients/*` -> Email Campaign Service

* `/pixel/{event_id}` -> Email Campaign Service (direct, or via gateway for tracking)
  

**5.2. User Management Service (UMS) API Endpoints:**


* `POST /api/auth/login`: Authenticate a user and return a session token (e.g., JWT).

* `POST /api/auth/logout`: Invalidate a user's session.

* `GET /api/users`: List all administrative users (requires 'super_admin' role).

* `POST /api/users`: Create a new user (requires 'super_admin' role).

* `GET /api/users/{id}`: Retrieve a single user's details.

* `PUT /api/users/{id}`: Update a user's details (password, role, name).

* `DELETE /api/users/{id}`: Deactivate/delete a user.

**5.3. Asset Inventory Service (AIS) API Endpoints:**

* **Asset Endpoints:**

    * `GET /api/assets`: List all assets with pagination, filtering (by status, category, user, location, purchase date), and sorting.

    * `POST /api/assets`: Create a new asset.

    * `GET /api/assets/{id}`: Retrieve a single asset's details.

    * `PUT /api/assets/{id}`: Update an asset's details.

    * `DELETE /api/assets/{id}`: Mark an asset as disposed (soft delete, updates status).

    * `GET /api/assets/{id}/history`: Retrieve the full audit log for a specific asset.

* **Category Endpoints:**

    * `GET /api/categories`: List all asset categories.

    * `POST /api/categories`: Create a new category.

    * `GET /api/categories/{id}`: Retrieve a single category.

    * `PUT /api/categories/{id}`: Update a category.

    * `DELETE /api/categories/{id}`: Delete a category.

* **Location Endpoints:**

    * `GET /api/locations`: List all locations.

    * `POST /api/locations`: Create a new location.

    * `GET /api/locations/{id}`: Retrieve a single location.

    * `PUT /api/locations/{id}`: Update a location.

    * `DELETE /api/locations/{id}`: Delete a location.

* **Supplier Endpoints:**

    * `GET /api/suppliers`: List all suppliers.

    * `POST /api/suppliers`: Create a new supplier.

    * `GET /api/suppliers/{id}`: Retrieve a single supplier.

    * `PUT /api/suppliers/{id}`: Update a supplier.

    * `DELETE /api/suppliers/{id}`: Delete a supplier.

* **Reports Endpoints:**

    * `GET /api/reports/assets-by-category`: Summary report of total assets per category.

    * `GET /api/reports/depreciation`: Asset depreciation report (requires specific business logic and configuration not detailed in schema).

    * `GET /api/reports/maintenance-schedule`: Upcoming maintenance report based on asset data.  

**5.4. Booking Service (BKS) API Endpoints:**

* **Resource Endpoints:**

    * `GET /api/bookings/resources`: List all bookable resources.

    * `POST /api/bookings/resources`: Create a new resource.

    * `GET /api/bookings/resources/{id}`: Retrieve a single resource's details.

    * `PUT /api/bookings/resources/{id}`: Update a resource.

    * `DELETE /api/bookings/resources/{id}`: Delete a resource.

    * `GET /api/bookings/resources/{id}/availability`: Check availability for a specific resource for a given date range.

* **Booking Endpoints:**

    * `GET /api/bookings`: List all bookings (with filtering by resource, user, date range, status).

    * `POST /api/bookings`: Create a new booking.

    * `GET /api/bookings/{id}`: Retrieve a single booking's details.

    * `PUT /api/bookings/{id}`: Update a booking (e.g., time, purpose).

    * `DELETE /api/bookings/{id}`: Cancel a booking.

**5.5. Email Campaign Service (ECS) API Endpoints:**

* **Campaign Endpoints:**

    * `GET /api/campaigns`: List all campaigns with filtering.

    * `POST /api/campaigns`: Create a new campaign (draft status).

    * `GET /api/campaigns/{id}`: Retrieve a single campaign's details.

    * `PUT /api/campaigns/{id}`: Update a campaign.

    * `DELETE /api/campaigns/{id}`: Delete a campaign (soft delete).

    * `POST /api/campaigns/{id}/send`: Manually trigger a campaign send (or update status to 'scheduled').

    * `GET /api/campaigns/{id}/reports`: Retrieve summary analytics for a campaign.

* **Template Endpoints:**

    * `GET /api/templates`: List all email templates.

    * `POST /api/templates`: Create a new email template.

    * `GET /api/templates/{id}`: Retrieve a single template.

    * `PUT /api/templates/{id}`: Update a template.

    * `DELETE /api/templates/{id}`: Delete a template.

* **Recipient Endpoints:**

    * `GET /api/recipients`: List all recipients.

    * `POST /api/recipients`: Add a new recipient.

    * `GET /api/recipients/{id}`: Retrieve a single recipient.

    * `PUT /api/recipients/{id}`: Update a recipient.

    * `DELETE /api/recipients/{id}`: Delete a recipient.

    * `POST /api/recipients/{id}/unsubscribe`: Mark a recipient as unsubscribed.

* **Tracking Endpoint (Accessed directly or via gateway, low authentication):**

    * `GET /pixel/{send_id}`: Tracking pixel URL for email open events.

6. Cross-Service Communication and Integration Points

Inter-service communication will leverage a combination of RESTful API calls for synchronous requests and a Message Broker (e.g., Kafka) for asynchronous events.

**6.1. User Management Service (UMS) as Central Authority:**
 

* All services will validate JWTs issued by UMS for authentication.

* Authorization decisions will be based on roles defined and managed by UMS. Services will query UMS for user role information as needed.

**6.2. Asset Inventory Service (AIS) Integrations:**

* **AIS -> Message Broker:**

    * Publishes `asset_status_changed` events (e.g., 'disposed', 'in_maintenance').

    * Publishes `low_stock_alert` events.

    * Publishes `warranty_expiry_alert` events.

* **AIS <- Message Broker:**

    * Subscribes to `booking_created` events from BKS to potentially link an asset to a booking or update its location/status (if a specific asset is being booked). This would be a *loose* coupling.

**6.3. Booking Service (BKS) Integrations:**

* **BKS -> Message Broker:**

    * Publishes `booking_created` events (for notifications and potential AIS linkage).

    * Publishes `booking_updated` events.

    * Publishes `booking_cancelled` events.

* **BKS -> UMS:**

    * Synchronous calls to retrieve user details (`full_name`, `email`) for booking confirmations.

* **BKS -> AIS:**

    * Synchronous calls to retrieve `locations` data if a resource needs to be tied to an existing asset location for validation or display.

    * If a `resource` in BKS directly represents an `asset` in AIS (`associated_asset_id` is populated), BKS might call AIS to query `asset` status before allowing a booking, or vice-versa. This needs careful design to avoid tight coupling. A better approach might be for BKS to react to `asset_status_changed` events and update its `resources` accordingly.

**6.4. Email Campaign Service (ECS) Integrations:**

* **ECS -> Message Broker:**

    * Publishes `email_send_success` events.

    * Publishes `email_send_failure` events.

    * Publishes `email_opened` events.

    * Publishes `email_replied` events (with `raw_reply_text`).

    * Publishes `campaign_error` events.

* **ECS <- Message Broker:**

    * Subscribes to `booking_created`, `booking_updated`, `booking_cancelled` events from BKS to send automated booking confirmations/reminders.

    * Subscribes to `asset_status_changed`, `low_stock_alert`, `warranty_expiry_alert` events from AIS to send administrative alerts.

* **ECS -> UMS:**

    * Synchronous calls to retrieve user emails for internal alerts/notifications.

    * Users for campaigns (recipients) are managed internally by ECS, but could be populated from a central directory if the platform grows.

**6.5. Asynchronous Workflow Examples:**

* **Booking Confirmation:**

    1.  User creates booking via UI.

    2.  SPA calls BKS API.

    3.  BKS creates booking, validates, saves to DB.

    4.  BKS publishes `booking_created` event to Message Broker.

    5.  ECS subscribes to `booking_created` event.

    6.  ECS's Campaign Executor picks up event, uses relevant template, sends confirmation email.

* **Asset Warranty Alert:**

    1.  AIS's Alerts Module periodically checks `assets` table.

    2.  If `warranty_expiry_date` is approaching, AIS publishes `warranty_expiry_alert` event to Message Broker.

    3.  ECS subscribes to `warranty_expiry_alert` event.

    4.  ECS sends an internal notification email to relevant administrators.

  

# Future Considerations and Scalability

* **Observability:** Implement robust distributed tracing (e.g., OpenTelemetry) to understand request flows across services.

* **API Versioning:** Plan for API versioning from the start (e.g., `/api/v1/assets`).

* **Caching:** Introduce caching layers (e.g., Redis) where appropriate, especially for frequently accessed read-heavy data.

* **Idempotency:** Design API endpoints to be idempotent for resilient communication in a distributed system.

* **Circuit Breakers & Retries:** Implement resilience patterns to handle service failures gracefully.

* **Data Synchronization Strategy:** For any shared conceptual data (like "locations" between AIS and BKS), decide on a clear ownership model and synchronization strategy (e.g., BKS makes API calls to AIS for location details, or AIS publishes location changes to Message Broker that BKS subscribes to and stores a local copy). For simplicity, initial design relies on direct lookups or conceptual linking.

* **External Integrations:** Consider future integrations with external systems (e.g., ERP for finance, HR systems for user data). This architecture allows for dedicated integration services.

* **Notification Service:** As notifications grow, consider extracting a dedicated Notification Service to handle various notification channels (email, SMS, in-app, Slack) instead of embedding all logic within ECS.


  