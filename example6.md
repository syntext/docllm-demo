# AngularCRM: Payment Orchestration Platform

## Technical Documentation

**Version 1.0**

---

## Table of Contents

1. Introduction
2. Framework and Technologies
3. System Architecture
4. Core Functionalities / Modules
5. Data Models and Storage
6. Integration Points and APIs
7. Security and Permissions
8. Conclusion

---

## 1. Introduction

AngularCRM is a robust payment orchestration platform built with Kotlin. This platform serves as a central hub for managing payment processing, risk mitigation, and customer management. It provides a flexible and scalable solution for handling various payment gateways, transaction types, and risk management strategies.

This technical documentation provides a comprehensive overview of the system architecture, core functionalities, data models, integration points, and security measures implemented in the AngularCRM platform. It is intended for technical stakeholders, developers, and system architects who need to understand the system's design and implementation.

---

## 2. Framework and Technologies

### 2.1 Core Technologies

The AngularCRM payment orchestration platform is built using the following core technologies:

- **Programming Language**: Kotlin
- **Application Framework**: Spring Boot
- **Build System**: Gradle (inferred from the project structure)

Kotlin was chosen as the primary programming language due to its modern features, null safety, and full interoperability with Java. These characteristics make it an excellent choice for developing a robust and maintainable payment processing system where reliability and type safety are critical.

### 2.2 Spring Ecosystem

The platform extensively leverages the Spring ecosystem, which provides a comprehensive framework for building enterprise applications:

- **Spring Boot**: Simplifies the setup and configuration of Spring applications, enabling rapid development and deployment.
- **Spring MVC**: Handles HTTP requests and responses, providing a solid foundation for the REST API.
- **Spring Data JPA**: Simplifies data access by reducing boilerplate code for database operations.
- **Spring Security**: Manages authentication and authorization throughout the application.
- **Spring Events**: Implements an event-driven architecture for loose coupling between components.
- **Spring Scheduling**: Manages scheduled tasks such as recurring orders and risk alert processing.

### 2.3 Persistence and Data Access

- **JPA/Hibernate**: Used for object-relational mapping (ORM) to simplify database interactions.
- **PostgreSQL**: Inferred as the primary relational database based on the use of JSONB types and specific SQL syntax.
- **JDBC**: Used for direct database access where more control is needed over queries.

### 2.4 Messaging and Integration

- **Apache ActiveMQ Artemis**: Used for asynchronous messaging, particularly for handling email notifications and transaction callbacks.
- **JMS (Java Message Service)**: Provides the messaging API for interacting with ActiveMQ Artemis.
- **RestTemplate**: Facilitates HTTP client operations for integrating with external services.
- **WebServiceTemplate**: Supports SOAP-based web service interactions, particularly for risk alert services.

### 2.5 Caching

The platform implements a multi-level caching strategy:

- **In-Memory Cache**: For frequently accessed, non-critical data.
- **Persistent Cache**: For data that needs to survive application restarts.

### 2.6 Security

- **JWT (JSON Web Tokens)**: Used for stateless authentication.
- **API Key Authentication**: Provides an alternative authentication mechanism for API access.
- **BCrypt**: Employed for secure password hashing.

### 2.7 Monitoring and Error Tracking

- **Sentry**: Integrated for error tracking and performance monitoring.
- **Micrometer**: Used for application metrics collection.
- **Logging**: Utilizes KotlinLogging for structured logging throughout the application.

### 2.8 Additional Libraries and Tools

- **Jackson**: For JSON serialization and deserialization.
- **FreeMarker**: Template engine used for generating dynamic content such as emails and invoices.
- **AWS SDK**: For S3 integration, used for storing and retrieving file content.
- **OpenCSV**: Used for parsing CSV files, particularly for risk alert data.
- **ShedLock**: Ensures that scheduled tasks are executed only once in a distributed environment.

The combination of these technologies creates a robust foundation for the payment orchestration platform, enabling it to handle complex payment flows, integrate with multiple external services, and provide reliable and secure payment processing capabilities.

---

## 3. System Architecture

### 3.1 Architectural Overview

The AngularCRM payment orchestration platform follows a modular, layered architecture with clear separation of concerns. This design promotes maintainability, testability, and scalability while allowing for flexible integration with various payment gateways and services.

### 3.2 Architectural Patterns

#### 3.2.1 Layered Architecture

The system implements a traditional layered architecture with the following layers:

1. **Presentation Layer**: Controllers that handle HTTP requests and responses.
2. **Service Layer**: Business logic encapsulated in service classes.
3. **Repository Layer**: Data access components that interact with the database.
4. **Domain Layer**: Entity models representing the business domain.

This layered approach ensures that each component has a single responsibility, making the system easier to understand, maintain, and extend.

#### 3.2.2 Event-Driven Architecture

The platform extensively uses an event-driven architecture to decouple components:

- **Event Publishers**: Components that publish events when significant state changes occur (e.g., order status changes, transaction status changes).
- **Event Listeners**: Components that subscribe to events and react accordingly.
- **Event Store**: Persistent storage for events, enabling event sourcing patterns.

This approach allows for loose coupling between components, making the system more resilient and easier to extend.

#### 3.2.3 Repository Pattern

The data access layer follows the repository pattern, providing a clean abstraction over the underlying data store:

- **Standard Repositories**: Extend Spring Data's `JpaRepository` or `CrudRepository` for common CRUD operations.
- **Custom Repositories**: Implement specialized query methods for more complex data access requirements.
- **Soft Delete Repositories**: Extend standard repositories to support soft deletion of entities.

#### 3.2.4 Factory Pattern

The system uses factory patterns to create complex objects:

- **Driver Factory**: Creates appropriate driver implementations based on configuration.
- **Matcher Factory**: Creates transaction matchers based on risk alert matcher types.

#### 3.2.5 Strategy Pattern

Strategy patterns are employed to handle varying behaviors:

- **Risk Mitigation Strategies**: Different approaches to risk assessment and mitigation.
- **Transaction Processing Strategies**: Various ways to process transactions based on gateway types.

### 3.3 System Modularity

The platform is organized into several core modules, each responsible for specific functionality:

#### 3.3.1 Common Module

The common module provides shared functionality used throughout the application:

- **Model**: Domain entities and data transfer objects.
- **Repository**: Data access components.
- **Service**: Common business logic.
- **Util**: Utility classes and helper functions.
- **Web**: Web-related components such as filters and interceptors.
- **Driver**: Integration with external services.
- **Validation**: Custom validation logic.

#### 3.3.2 Feature Modules

Feature modules encapsulate specific business capabilities:

- **Orders**: Order creation, processing, and management.
- **Customers**: Customer management and subscription handling.
- **Transactions**: Transaction processing and management.
- **Risk Notifications**: Risk alert processing and notification.
- **Risk Prevention**: Fraud prevention and risk mitigation.
- **Invoicing**: Invoice generation and management.
- **Notifications**: Email and notification handling.
- **Webhooks**: Webhook processing and management.
- **Routing**: Request routing based on rules.
- **Templating**: Template rendering for emails and documents.
- **Languages**: Internationalization support.
- **Fees**: Fee calculation and management.
- **Exchange Rates**: Currency exchange rate handling.
- **Products**: Product management.

### 3.4 Communication Patterns

The modules communicate through several mechanisms:

#### 3.4.1 Direct Method Calls

Services often directly call methods on other services when tight coupling is acceptable or necessary.

#### 3.4.2 Event-Based Communication

For looser coupling, the system uses events:

- **Application Events**: Spring's `ApplicationEvent` for synchronous event handling.
- **JMS Messages**: For asynchronous communication, particularly for time-consuming operations.

#### 3.4.3 Repository Access

Services access data through repositories, providing a clean abstraction over the data store.

### 3.5 Configuration Management

The system uses Spring's configuration capabilities:

- **Property-Based Configuration**: External configuration through properties files.
- **Database Configuration**: Dynamic configuration stored in the database.
- **Profile-Based Configuration**: Different configurations for different environments (e.g., development, production).

### 3.6 Asynchronous Processing

The platform employs several approaches to asynchronous processing:

- **@Async Annotations**: For non-blocking method execution.
- **Scheduled Tasks**: For periodic jobs such as risk alert processing.
- **Message Queues**: For reliable asynchronous communication.

### 3.7 Error Handling and Resilience

The system implements robust error handling:

- **Global Exception Handlers**: Centralized handling of exceptions.
- **Retry Mechanisms**: Automatic retry for transient failures.
- **Circuit Breakers**: Prevention of cascading failures.
- **Fallbacks**: Graceful degradation when services are unavailable.

### 3.8 Deployment Architecture

While the specific deployment architecture is not explicitly defined in the codebase, the system is designed to support:

- **Containerization**: Suitable for deployment in containers such as Docker.
- **Horizontal Scaling**: Stateless design allows for multiple instances.
- **Cloud Deployment**: Compatible with cloud platforms.

The modular architecture of the AngularCRM payment orchestration platform provides a solid foundation for building a flexible, maintainable, and scalable system. The clear separation of concerns and well-defined communication patterns allow for easy extension and integration with various payment gateways and services.

---

## 4. Core Functionalities / Modules

### 4.1 Order Management

The order management module is central to the platform's functionality, handling the creation, processing, and management of payment orders.

#### 4.1.1 Order Creation

The order creation process begins with an HTTP request to the order controller, which:

1. Validates the incoming order request
2. Maps credit card information
3. Populates additional fields from HTTP headers
4. Delegates to the order service for processing

The order service then:

1. Creates or updates customer information
2. Resolves product details
3. Checks for duplicate orders
4. Enforces risk checks
5. Routes the order to the appropriate payment gateway
6. Creates and processes the initial transaction

This modular approach allows for flexible order handling while maintaining a clean separation of concerns.

#### 4.1.2 Order Processing

Once created, orders go through a processing workflow:

1. Transaction creation and processing
2. Status updates based on transaction results
3. Event publication for status changes
4. Webhook triggering for external notifications

The system supports various order types, including:

- **Initial Orders**: First-time purchases
- **Recurring Orders**: Subscription-based purchases
- **Retry Orders**: Reattempts of failed orders

#### 4.1.3 Order Reversal

The platform provides comprehensive support for order reversals:

1. Manual reversals initiated by API requests
2. Automatic reversals triggered by risk alerts
3. Transaction void or refund processing
4. Status updates and event publication

### 4.2 Customer Management

The customer management module handles customer data and subscription management.

#### 4.2.1 Customer Creation

Customers are created during the order process:

1. Customer data extraction from order request
2. Risk enrichment with additional information
3. Secure storage of sensitive data
4. Association with outlets and inventory items

#### 4.2.2 Subscription Management

For recurring products, the system manages subscriptions:

1. Subscription creation based on billing strategy
2. Recurring order scheduling
3. Subscription cancellation handling
4. Card expiration management

#### 4.2.3 Customer Status Management

The platform tracks customer statuses:

1. Status updates based on order and transaction events
2. Event publication for status changes
3. Webhook triggering for external notifications

### 4.3 Transaction Processing

The transaction processing module is responsible for handling payment transactions through various gateways.

#### 4.3.1 Transaction Types

The system supports multiple transaction types:

- **Sale**: Standard payment transaction
- **3D Secure Sale**: Payment with 3D Secure authentication
- **Authorization**: Fund reservation without capture
- **Capture**: Fund collection after authorization
- **Refund**: Return of funds to the customer
- **Void**: Cancellation of a previous transaction

#### 4.3.2 Transaction Workflow

Transactions follow a defined workflow:

1. Creation with initial status
2. Processing through the appropriate payment gateway
3. Status updates based on gateway responses
4. Event publication for status changes

#### 4.3.3 Transaction Callbacks

The platform handles asynchronous callbacks from payment gateways:

1. Callback reception through dedicated endpoints
2. Message queuing for reliable processing
3. Transaction updates based on callback data
4. Order status updates and event publication

### 4.4 Risk Management

The risk management module provides comprehensive risk assessment and mitigation capabilities.

#### 4.4.1 Risk Prevention

The system implements several risk prevention strategies:

1. Customer blacklisting
2. Velocity checks for suspicious activity
3. BIN list validation
4. Country code validation
5. IP address validation

#### 4.4.2 Risk Alert Processing

The platform processes risk alerts from various sources:

1. Alert fetching from external providers
2. Alert matching with transactions
3. Automatic order reversal when necessary
4. Customer subscription cancellation when required

#### 4.4.3 Dispute Handling

The system manages payment disputes:

1. Dispute data reception from payment gateways
2. Dispute matching with transactions
3. Event publication for dispute creation
4. Automatic subscription cancellation when necessary

### 4.5 Notification System

The notification system handles various types of communications.

#### 4.5.1 Email Notifications

The platform sends email notifications for various events:

1. Order confirmation
2. Payment confirmation
3. Subscription renewal
4. Card expiration
5. Invoice generation

#### 4.5.2 Webhook Notifications

The system triggers webhooks to notify external systems:

1. Rule-based webhook execution
2. Dynamic URL generation
3. HTTP request sending
4. Response logging

### 4.6 Invoicing

The invoicing module generates and manages invoices for orders.

#### 4.6.1 Invoice Generation

Invoices are generated based on order events:

1. Template selection based on invoice type
2. Data gathering from order and customer
3. Template rendering with gathered data
4. Invoice storage and association with order

#### 4.6.2 Invoice Types

The system supports different invoice types:

- **Debit Invoices**: For successful payments
- **Credit Invoices**: For refunds or reversals

### 4.7 Routing System

The routing system directs requests to appropriate processing components.

#### 4.7.1 Rule-Based Routing

Routing decisions are based on configurable rules:

1. Rule evaluation against request criteria
2. Outlet configuration selection
3. Payment gateway selection
4. Logging of routing decisions

#### 4.7.2 Rejection Rules

The system can reject requests based on rules:

1. Global rejection rules
2. Customer-specific rejection rules
3. Card-based rejection rules
4. Email-based rejection rules

### 4.8 Scheduling System

The scheduling system manages various periodic tasks.

#### 4.8.1 Recurring Order Processing

The platform schedules and processes recurring orders:

1. Job scheduling based on billing cycle
2. Order creation at scheduled time
3. Transaction processing
4. Status updates and event publication

#### 4.8.2 Retry Order Processing

Failed orders can be retried automatically:

1. Retry scheduling based on strategy
2. Order creation with adjusted pricing
3. Transaction processing
4. Status updates and event publication

#### 4.8.3 Risk Alert Processing

The system periodically processes risk alerts:

1. Alert fetching from external providers
2. Alert matching with transactions
3. Automatic actions based on alert type
4. Status updates and event publication

### 4.9 Drivers / Third-Party Integrations

The platform integrates with various external services through specialized drivers.

#### 4.9.1 Payment Gateway Drivers

The system supports multiple payment gateways:

- **Dummy Gateway**: For testing and development
- **ACI**: For ACI payment processing
- **MaxPay**: For MaxPay payment processing
- **XPG**: For XPG payment processing

Each driver implements a common interface, providing:

1. Transaction processing methods
2. Callback handling
3. Dispute fetching
4. Risk alert fetching

#### 4.9.2 Risk Mitigation Drivers

The platform integrates with risk mitigation services:

- **Dummy Risk Mitigator**: For testing and development
- **BinCodes.com**: For BIN validation
- **IIN List**: For IIN (Issuer Identification Number) validation
- **MinFraud**: For fraud detection

These drivers provide:

1. Risk assessment based on customer and order data
2. BIN resolution for card information
3. Fraud score calculation

#### 4.9.3 Risk Notification Drivers

The system processes risk alerts from various providers:

- **Payment Gateway Providers**: For gateway-specific alerts
- **Ethoca**: For Ethoca alerts
- **Verifi**: For Verifi alerts
- **Rapyd**: For Rapyd alerts
- **Paynetics**: For Paynetics alerts
- **Ramtool**: For Ramtool alerts

These drivers handle:

1. Alert fetching from external APIs
2. File upload parsing for alerts
3. Alert matching with transactions

#### 4.9.4 Email Service Drivers

The platform sends emails through various providers:

- **SMTP**: For standard email sending
- **Mailgun**: For Mailgun API integration
- **Dummy Drivers**: For testing and development

These drivers provide:

1. Email message construction
2. Attachment handling
3. Template rendering
4. Email sending

#### 4.9.5 Currency Exchange Rate Drivers

The system integrates with currency exchange rate services:

- **ExchangeRates.io**: For exchange rate data
- **API Layer**: For alternative exchange rate data

These drivers offer:

1. Exchange rate resolution between currencies
2. Caching of exchange rate data
3. Fallback mechanisms for unavailable data

#### 4.9.6 Geo Location Services

The platform uses geo-location services to:

1. Resolve country codes from IP addresses
2. Validate billing addresses
3. Enforce country-specific rules

### 4.10 Templating System

The templating system generates dynamic content for various purposes.

#### 4.10.1 Template Types

The platform supports different template types:

- **Email Templates**: For email notifications
- **Invoice Templates**: For invoice generation
- **Webhook Templates**: For dynamic webhook URLs

#### 4.10.2 Template Rendering

Templates are rendered using FreeMarker:

1. Data gathering from relevant sources
2. Template loading from database or files
3. Template rendering with gathered data
4. Error handling for rendering issues

### 4.11 Fee Management

The fee management module handles various fees associated with transactions.

#### 4.11.1 Fee Types

The system supports different fee types:

- **Fixed Fees**: Flat amount fees
- **Percentage Fees**: Fees based on transaction amount

#### 4.11.2 Fee Events

Fees can be applied to various events:

- **Transaction Events**: Sales, refunds, chargebacks
- **Dispute Events**: Chargebacks, representments
- **Fraud Events**: Fraud reports, alerts

#### 4.11.3 Fee Application

The platform applies fees based on rules:

1. Fee set selection based on outlet configuration
2. Fee filtering based on event type
3. Fee calculation based on fee type
4. Fee application and recording

The core functionalities of the AngularCRM payment orchestration platform provide a comprehensive solution for payment processing, risk management, and customer management. The modular design allows for easy extension and integration with various external services, while the event-driven architecture ensures loose coupling between components.

---

## 5. Data Models and Storage

### 5.1 Database Overview

The AngularCRM payment orchestration platform uses a relational database system (inferred to be PostgreSQL) for persistent storage. The database schema is designed to support the complex relationships between entities while maintaining data integrity and performance.

### 5.2 Core Entities

#### 5.2.1 Order Entity

The Order entity is central to the platform, representing a customer's purchase:

- **Primary Attributes**: ID, status, amount, currency, creation timestamp
- **Relationships**:
  - Many-to-one with Customer
  - One-to-many with OrderItem
  - One-to-many with Transaction
- **Special Features**:
  - JSON attributes for flexible metadata storage
  - Soft deletion support
  - Status tracking with timestamps

#### 5.2.2 Customer Entity

The Customer entity stores information about the system's users:

- **Primary Attributes**: ID, email, name, status, card details (encrypted)
- **Relationships**:
  - One-to-many with Order
  - Many-to-one with OutletConfig
- **Special Features**:
  - Secure storage of sensitive data
  - Status tracking for subscription management
  - IP address and browser information for risk assessment

#### 5.2.3 Transaction Entity

The Transaction entity represents payment transactions:

- **Primary Attributes**: ID, type, status, amount, currency, gateway reference
- **Relationships**:
  - Many-to-one with Order
  - Many-to-one with PaymentGatewayConfig
- **Special Features**:
  - Status tracking with timestamps
  - Response code storage for troubleshooting
  - JSON attributes for gateway-specific data

#### 5.2.4 Product Entity

The Product entity defines items that can be purchased:

- **Primary Attributes**: ID, name, description, type
- **Relationships**:
  - One-to-many with ProductPrice
  - Many-to-one with BillingStrategy
- **Special Features**:
  - Support for recurring products
  - Multi-currency pricing
  - Trial period configuration

#### 5.2.5 OutletConfig Entity

The OutletConfig entity stores configuration for merchant outlets:

- **Primary Attributes**: ID, key, description
- **Relationships**:
  - One-to-many with PaymentGatewayConfig
  - One-to-many with RiskMitigatorConfig
  - One-to-many with Customer
  - Many-to-many with FeeSet
  - Many-to-many with RuleSet
- **Special Features**:
  - Flexible configuration for different merchant needs
  - Support for multiple payment gateways
  - Risk mitigation configuration

### 5.3 Configuration Entities

#### 5.3.1 PaymentGatewayConfig Entity

Stores configuration for payment gateways:

- **Primary Attributes**: ID, type, description, API credentials
- **Relationships**:
  - Many-to-one with OutletConfig
  - One-to-many with Transaction
- **Special Features**:
  - Secure storage of API credentials
  - Feature toggles for different transaction types
  - Gateway-specific configuration in JSON format

#### 5.3.2 RiskMitigatorConfig Entity

Configures risk mitigation services:

- **Primary Attributes**: ID, type, description, API credentials
- **Relationships**:
  - Many-to-one with OutletConfig
- **Special Features**:
  - Secure storage of API credentials
  - Configuration for different risk mitigation strategies
  - Service-specific settings in JSON format

#### 5.3.3 EmailServiceConfig Entity

Defines email service configurations:

- **Primary Attributes**: ID, type, provider, credentials
- **Relationships**:
  - One-to-one with Outlet
- **Special Features**:
  - Support for multiple email providers
  - Secure storage of credentials
  - Provider-specific configuration

#### 5.3.4 CurrencyExchangeRatesConfig Entity

Configures currency exchange rate services:

- **Primary Attributes**: ID, driver type, API credentials
- **Special Features**:
  - Support for multiple exchange rate providers
  - Secure storage of API credentials
  - Provider-specific configuration

### 5.4 Risk Management Entities

#### 5.4.1 RiskAlert Entity

Represents risk alerts from various sources:

- **Primary Attributes**: ID, type, source, provider, status, parsed data
- **Relationships**:
  - Many-to-many with Transaction through RiskAlertMatch
- **Special Features**:
  - JSON storage for parsed and raw data
  - Status tracking for alert processing
  - Support for different alert types and sources

#### 5.4.2 Dispute Entity

Stores dispute information:

- **Primary Attributes**: ID, type, status, amount, currency
- **Relationships**:
  - Many-to-one with Transaction
  - Many-to-one with PaymentGatewayConfig
- **Special Features**:
  - JSON storage for dispute details
  - Status tracking for dispute processing
  - Support for different dispute types

#### 5.4.3 VelocityRule Entity

Defines rules for velocity checks:

- **Primary Attributes**: ID, type, limit, time frame
- **Relationships**:
  - Many-to-many with RuleSet
- **Special Features**:
  - Support for different velocity check types
  - Configurable limits and time frames
  - Integration with rule sets

### 5.5 Notification and Webhook Entities

#### 5.5.1 NotificationRule Entity

Configures notification rules:

- **Primary Attributes**: ID, event type, description, template
- **Relationships**:
  - Many-to-many with RuleSet
  - Many-to-one with NotificationTemplate
- **Special Features**:
  - Support for different notification events
  - Offset configuration for scheduled notifications
  - Order sequence filtering

#### 5.5.2 WebHookRule Entity

Defines webhook rules:

- **Primary Attributes**: ID, type, description
- **Relationships**:
  - One-to-many with WebHookRuleUrlConfig
  - Many-to-many with RuleSet
- **Special Features**:
  - Support for different webhook events
  - Multiple URL configurations per rule
  - Criteria-based execution

#### 5.5.3 NotificationJob Entity

Represents scheduled notification jobs:

- **Primary Attributes**: ID, event type, method type, content
- **Relationships**:
  - Many-to-one with ApplicationJob
  - Many-to-one with Customer
- **Special Features**:
  - Status tracking for job execution
  - Support for attachments
  - Integration with the application job system

### 5.6 Scheduling Entities

#### 5.6.1 ApplicationJob Entity

The base entity for all scheduled jobs:

- **Primary Attributes**: ID, type, status, scheduled time
- **Relationships**:
  - One-to-one with RecurringOrderJob
  - One-to-one with RetryOrderJob
  - One-to-one with NotificationJob
- **Special Features**:
  - Status tracking for job execution
  - Scheduling with timestamps
  - Support for different job types

#### 5.6.2 RecurringOrderJob Entity

Represents scheduled recurring orders:

- **Primary Attributes**: ID, sequence number
- **Relationships**:
  - One-to-one with ApplicationJob
  - Many-to-one with Order
  - Many-to-one with Customer
- **Special Features**:
  - Sequence tracking for recurring orders
  - Integration with the application job system

#### 5.6.3 RetryOrderJob Entity

Represents scheduled retry orders:

- **Primary Attributes**: ID
- **Relationships**:
  - One-to-one with ApplicationJob
  - Many-to-one with Order
  - Many-to-one with Customer
- **Special Features**:
  - Integration with the application job system
  - Connection to the original order

### 5.7 Financial Entities

#### 5.7.1 Fee Entity

Defines fees for various events:

- **Primary Attributes**: ID, name, type, event type, amount
- **Relationships**:
  - One-to-many with FeeSetFee
- **Special Features**:
  - Support for fixed and percentage fees
  - Association with specific event types
  - Optional disabling with timestamp

#### 5.7.2 FeeSet Entity

Groups fees for easier management:

- **Primary Attributes**: ID, description
- **Relationships**:
  - One-to-many with FeeSetFee
  - Many-to-many with OutletConfig
- **Special Features**:
  - Grouping of related fees
  - Association with outlet configurations

#### 5.7.3 AppliedFee Entity

Records fees applied to events:

- **Primary Attributes**: ID, event ID, event type, amount
- **Special Features**:
  - Timestamp for fee application
  - Connection to the triggering event
  - Amount calculation based on fee type

#### 5.7.4 Invoice Entity

Represents invoices generated for orders:

- **Primary Attributes**: ID, type, name, content
- **Relationships**:
  - Many-to-one with Order
- **Special Features**:
  - Support for different invoice types
  - Storage of rendered invoice content
  - Timestamp for invoice creation

### 5.8 Routing and Rules Entities

#### 5.8.1 OrderRoutingRule Entity

Defines rules for order routing:

- **Primary Attributes**: ID, description, rule type, criteria
- **Relationships**:
  - Many-to-many with OutletConfig
- **Special Features**:
  - JSON storage for routing criteria
  - Support for different rule types
  - Association with outlet configurations

#### 5.8.2 RuleSet Entity

Groups rules for easier management:

- **Primary Attributes**: ID, description
- **Relationships**:
  - Many-to-many with WebHookRule
  - Many-to-many with NotificationRule
  - Many-to-many with VelocityRule
  - Many-to-many with OutletConfig
- **Special Features**:
  - Grouping of related rules
  - Association with outlet configurations

#### 5.8.3 RoutingLog Entity

Records routing decisions:

- **Primary Attributes**: ID, customer ID, action type, criteria
- **Relationships**:
  - Many-to-one with OutletConfig (source)
  - Many-to-one with OutletConfig (destination)
  - Many-to-one with OrderRoutingRule
- **Special Features**:
  - JSON storage for routing criteria
  - Timestamps for routing decisions
  - Connection to the applied rule

### 5.9 Internationalization Entities

#### 5.9.1 Language Entity

Stores language definitions:

- **Primary Attributes**: ID, language code, country code, key, value
- **Special Features**:
  - Support for multiple languages and countries
  - Key-value storage for translations
  - Optional disabling with timestamp

#### 5.9.2 VatRule Entity

Defines VAT rates for different countries:

- **Primary Attributes**: ID, country code, VAT rate
- **Special Features**:
  - Country-specific VAT rates
  - Optional disabling with timestamp
  - Support for different rate formats

### 5.10 Data Storage Considerations

#### 5.10.1 Soft Deletion

Many entities support soft deletion:

- **Approach**: Instead of physically removing records, a `deletedOn` timestamp is set
- **Benefits**:
  - Data retention for auditing and reporting
  - Prevention of accidental data loss
  - Simplified data recovery
- **Implementation**: Custom repositories extend `SoftDeleteCrudRepository`

#### 5.10.2 JSON Storage

The platform uses JSON storage for flexible data:

- **Approach**: Attributes that vary by context are stored as JSON
- **Benefits**:
  - Schema flexibility for different requirements
  - Support for complex nested structures
  - Reduced need for table joins
- **Implementation**: Hibernate's JSON type support

#### 5.10.3 Encryption

Sensitive data is encrypted:

- **Approach**: Credit card details and API credentials are encrypted
- **Benefits**:
  - Protection of sensitive information
  - Compliance with security standards
  - Reduced risk of data breaches
- **Implementation**: RSA encryption with secure key management

#### 5.10.4 Caching

The platform implements caching for performance:

- **Approach**: Both in-memory and persistent caches are used
- **Benefits**:
  - Reduced database load
  - Improved response times
  - Resilience to database unavailability
- **Implementation**: Custom cache managers with TTL support

The data model of the AngularCRM payment orchestration platform is designed to support the complex relationships and requirements of a payment processing system. The use of soft deletion, JSON storage, encryption, and caching provides a flexible and secure foundation for the platform's functionality.

---

## 6. Integration Points and APIs

### 6.1 REST API Endpoints

The AngularCRM payment orchestration platform exposes a comprehensive set of REST API endpoints for integration with front-end applications and external systems.

#### 6.1.1 Order Management Endpoints

- **Create Order**
  - **Path**: `/api/order`
  - **Method**: POST
  - **Purpose**: Creates a new order with payment processing
  - **Authentication**: API key
  - **Request**: Order details including items, customer information, and payment method
  - **Response**: Order result with optional redirect information

- **Complete Order**
  - **Path**: `/api/order/complete/{orderId}`
  - **Method**: GET/POST
  - **Purpose**: Completes a pending order, typically after 3D Secure authentication
  - **Authentication**: None (relies on transaction ID security)
  - **Response**: Redirect to configured return URL

- **Order Status**
  - **Path**: `/api/order/status/{orderId}`
  - **Method**: GET
  - **Purpose**: Retrieves the current status of an order
  - **Authentication**: API key
  - **Response**: Order status information

- **Order Conversion**
  - **Path**: `/api/order/conversion/{orderId}`
  - **Method**: GET
  - **Purpose**: Checks if an order counts as a conversion
  - **Authentication**: API key
  - **Response**: Conversion status information

- **Refund Order**
  - **Path**: `/api/order/refund`
  - **Method**: POST
  - **Purpose**: Initiates a refund for an order
  - **Authentication**: API key
  - **Request**: Order ID to refund
  - **Response**: Refund result information

#### 6.1.2 Customer Management Endpoints

- **Stop Subscription**
  - **Path**: `/api/customer/subscription/stop`
  - **Method**: POST
  - **Purpose**: Stops a customer's subscription
  - **Authentication**: API key
  - **Request**: Customer ID
  - **Response**: Success confirmation

- **Public Stop Subscription**
  - **Path**: `/public/customer/subscription/stop`
  - **Method**: POST
  - **Purpose**: Allows customers to request subscription cancellation
  - **Authentication**: None (email and card validation)
  - **Request**: Email, card last four digits, and outlet key
  - **Response**: Confirmation message

- **Confirm Subscription Stop**
  - **Path**: `/public/customer/subscription/stop/confirm`
  - **Method**: GET
  - **Purpose**: Confirms subscription cancellation via token
  - **Authentication**: Token in URL
  - **Parameters**: Email and token
  - **Response**: Success or error message

- **Blacklist Customer**
  - **Path**: `/api/customer/blacklist`
  - **Method**: POST
  - **Purpose**: Adds a customer to the blacklist
  - **Authentication**: API key
  - **Request**: Customer ID
  - **Response**: Success confirmation

#### 6.1.3 Payment Gateway Callback Endpoints

- **ACI Callback**
  - **Path**: `/gateway/aci/callback`
  - **Method**: POST
  - **Purpose**: Receives callbacks from ACI payment gateway
  - **Authentication**: Gateway-specific validation
  - **Request**: Transaction details from gateway
  - **Response**: Acknowledgment

- **MaxPay Callback**
  - **Path**: `/gateway/maxpay/callback`
  - **Method**: POST
  - **Purpose**: Receives callbacks from MaxPay gateway
  - **Authentication**: Gateway-specific validation
  - **Request**: Transaction details from gateway
  - **Response**: Acknowledgment

- **XPG Callback**
  - **Path**: `/gateway/xpg/callback`
  - **Method**: POST
  - **Purpose**: Receives callbacks from XPG gateway
  - **Authentication**: Gateway-specific validation
  - **Request**: Transaction details from gateway
  - **Response**: Acknowledgment

#### 6.1.4 Public Information Endpoints

- **Language Definitions**
  - **Path**: `/public/languages`
  - **Method**: GET
  - **Purpose**: Retrieves language definitions
  - **Authentication**: None
  - **Parameters**: Outlet config key, country code, language code, optional keys
  - **Response**: Language definitions

- **Product Price Terms**
  - **Path**: `/public/product/terms`
  - **Method**: GET
  - **Purpose**: Retrieves product pricing terms
  - **Authentication**: None
  - **Parameters**: Outlet config key, item keys, country code
  - **Response**: Product price terms information

### 6.2 Message Queue Integration

The platform uses Apache ActiveMQ Artemis for asynchronous messaging.

#### 6.2.1 Email Queue

- **Queue Name**: `emails`
- **Purpose**: Handles email message delivery
- **Producer**: Email event producer service
- **Consumer**: Email aware event listener
- **Message Format**: Email message DTO with recipient, sender, subject, body, and attachments
- **Processing**: Asynchronous email sending with retry capability

#### 6.2.2 Transaction Callback Queue

- **Queue Name**: `transaction.callbacks`
- **Purpose**: Processes gateway callbacks
- **Producer**: Gateway callback controllers
- **Consumer**: Transaction callback service
- **Message Format**: Gateway callback message with transaction ID and payload
- **Processing**: Asynchronous transaction updates

#### 6.2.3 Risk Alert Queue

- **Queue Name**: `risk.alerts`
- **Purpose**: Processes risk alerts
- **Producer**: Risk alert scheduler
- **Consumer**: Risk alert aware event listener
- **Message Format**: Risk alert ID
- **Processing**: Asynchronous risk alert processing

#### 6.2.4 Risk Alert Fetch Queue

- **Queue Name**: `risk.alert.fetching`
- **Purpose**: Fetches risk alerts from external providers
- **Producer**: Risk alert scheduler
- **Consumer**: Risk alert aware event listener
- **Message Format**: Risk alert fetch execution message DTO
- **Processing**: Asynchronous risk alert fetching

#### 6.2.5 Dead Letter Queue

- **Queue Name**: `dead.letters`
- **Purpose**: Stores messages that couldn't be processed
- **Producer**: Various services when processing fails
- **Consumer**: Manual intervention
- **Message Format**: Original message with error context
- **Processing**: Manual review and reprocessing

### 6.3 External Service Integration

#### 6.3.1 Payment Gateway Integration

The platform integrates with various payment gateways:

- **ACI Gateway**
  - **Integration Type**: REST API
  - **Authentication**: API key and secret
  - **Features**: Sale, 3D Secure, refund, void
  - **Callback Handling**: Asynchronous via webhook

- **MaxPay Gateway**
  - **Integration Type**: REST API
  - **Authentication**: API key and secret
  - **Features**: Sale, 3D Secure, refund, void, disputes, fraud reports
  - **Callback Handling**: Asynchronous via webhook

- **XPG Gateway**
  - **Integration Type**: REST API
  - **Authentication**: API key and signature
  - **Features**: Sale, 3D Secure, refund, void
  - **Callback Handling**: Asynchronous via webhook

#### 6.3.2 Risk Service Integration

The platform integrates with risk mitigation services:

- **BinCodes.com**
  - **Integration Type**: REST API
  - **Authentication**: API key
  - **Features**: BIN lookup, card issuer information
  - **Data Flow**: Synchronous request-response

- **IIN List**
  - **Integration Type**: REST API
  - **Authentication**: API key
  - **Features**: IIN validation, card type information
  - **Data Flow**: Synchronous request-response

- **MinFraud**
  - **Integration Type**: REST API
  - **Authentication**: API key
  - **Features**: Fraud scoring, risk assessment
  - **Data Flow**: Synchronous request-response

#### 6.3.3 Risk Alert Integration

The platform integrates with risk alert providers:

- **Ethoca**
  - **Integration Type**: SOAP API
  - **Authentication**: Username and password
  - **Features**: Chargeback alerts, fraud alerts
  - **Data Flow**: Periodic polling

- **Verifi**
  - **Integration Type**: File upload and API
  - **Authentication**: API key
  - **Features**: Dispute alerts, RDR (Rapid Dispute Resolution)
  - **Data Flow**: Periodic polling and file processing

- **Rapyd, Paynetics, Ramtool**
  - **Integration Type**: File upload
  - **Authentication**: Varies by provider
  - **Features**: Dispute and fraud alerts
  - **Data Flow**: File processing

#### 6.3.4 Email Service Integration

The platform integrates with email service providers:

- **SMTP**
  - **Integration Type**: SMTP protocol
  - **Authentication**: Username and password
  - **Features**: Email sending with attachments
  - **Data Flow**: Asynchronous via queue

- **Mailgun**
  - **Integration Type**: REST API
  - **Authentication**: API key
  - **Features**: Email sending with attachments
  - **Data Flow**: Asynchronous via queue

#### 6.3.5 Currency Exchange Rate Integration

The platform integrates with currency exchange rate services:

- **ExchangeRates.io**
  - **Integration Type**: REST API
  - **Authentication**: API key
  - **Features**: Currency conversion rates
  - **Data Flow**: Synchronous with caching

- **API Layer**
  - **Integration Type**: REST API
  - **Authentication**: API key
  - **Features**: Currency conversion rates
  - **Data Flow**: Synchronous with caching

#### 6.3.6 AWS S3 Integration

The platform integrates with AWS S3 for file storage:

- **Integration Type**: AWS SDK
- **Authentication**: Access key and secret
- **Features**: File upload, download, and management
- **Data Flow**: Synchronous request-response
- **Usage**: Storing and retrieving risk alert file uploads

### 6.4 Webhook Integration

The platform provides webhook capabilities for external system integration:

#### 6.4.1 Webhook Events

- **Order Paid**: Triggered when an order is successfully paid
- **Order Paid Initial**: Triggered for the first payment of a recurring order
- **Order Paid Recurring**: Triggered for subsequent payments of a recurring order
- **Order Failed**: Triggered when an order payment fails
- **Customer Subscribed**: Triggered when a customer subscribes to a recurring product
- **Customer Unsubscribed**: Triggered when a customer cancels a subscription

#### 6.4.2 Webhook Configuration

- **Rule-Based**: Webhooks are triggered based on configurable rules
- **URL Templates**: Support for dynamic URL generation with template variables
- **Criteria Evaluation**: Rules can include complex criteria for execution
- **Multiple URLs**: Each rule can trigger multiple URLs
- **Logging**: Comprehensive logging of webhook execution

#### 6.4.3 Webhook Data Flow

1. Event occurs in the system (e.g., order status change)
2. Event listener checks for applicable webhook rules
3. Rules are evaluated against event data
4. Matching rules trigger HTTP requests to configured URLs
5. Responses are logged for troubleshooting

### 6.5 Internal Event System

The platform uses an event-driven architecture for internal communication:

#### 6.5.1 Event Types

- **Order Status Change**: Triggered when an order's status changes
- **Transaction Status Change**: Triggered when a transaction's status changes
- **Customer Status Change**: Triggered when a customer's status changes
- **Dispute Created**: Triggered when a dispute is created
- **Invoice Created**: Triggered when an invoice is generated
- **Recurring Order Job Created**: Triggered when a recurring order job is scheduled

#### 6.5.2 Event Handling

1. Events are published by services when significant state changes occur
2. Event listeners subscribe to specific event types
3. When an event occurs, all relevant listeners are notified
4. Listeners perform actions based on the event data
5. Events are optionally stored in an event store for auditing

#### 6.5.3 Event Store

The platform maintains an event store:

- **Purpose**: Records significant events for auditing and event sourcing
- **Data**: Event type, aggregate ID, payload, metadata, user ID
- **Usage**: Audit trails, troubleshooting, event replay

The integration points and APIs of the AngularCRM payment orchestration platform provide a comprehensive framework for connecting with front-end applications, external systems, and third-party services. The combination of REST APIs, message queues, external service integration, webhooks, and internal events creates a flexible and robust ecosystem for payment processing and risk management.

---

## 7. Security and Permissions

### 7.1 Authentication Mechanisms

The AngularCRM payment orchestration platform implements multiple authentication mechanisms to secure access to its APIs and services.

#### 7.1.1 API Key Authentication

The primary authentication method for API access:

- **Implementation**: Custom filter that intercepts HTTP requests
- **Key Storage**: API keys are stored in the User entity
- **Key Validation**: The AuthService resolves and validates API keys
- **Header**: API keys are provided in the `X-API-KEY` header
- **Security Measures**:
  - Keys are securely hashed in the database
  - Keys can be disabled or deleted
  - Failed authentication attempts are logged

#### 7.1.2 Gateway Callback Authentication

For payment gateway callbacks:

- **Implementation**: Gateway-specific validation in callback controllers
- **Authentication Methods**:
  - **Signature Validation**: For gateways that sign their requests
  - **IP Whitelisting**: For gateways with fixed IP ranges
  - **Shared Secret**: For gateways that use a shared secret
  - **Transaction ID Validation**: Ensuring the transaction exists

#### 7.1.3 Public Endpoint Security

For endpoints that need to be publicly accessible:

- **Implementation**: Specific security configurations for public paths
- **Protection Measures**:
  - Rate limiting to prevent abuse
  - CAPTCHA for sensitive operations
  - Email verification for subscription management
  - Card information validation for customer identification

### 7.2 Authorization Framework

The platform implements a role-based access control (RBAC) system:

#### 7.2.1 User Roles

- **ADMIN**: Full system access
- **MERCHANT**: Access to specific outlet configurations
- **API**: Limited access for API integrations
- **SUPPORT**: Read-only access for customer support

#### 7.2.2 Permission Enforcement

- **Implementation**: Spring Security's authorization framework
- **Enforcement Points**:
  - Controller method annotations
  - Security expressions in configuration
  - Programmatic security checks in services
- **Context Propagation**: Security context is maintained across asynchronous operations

#### 7.2.3 Object-Level Security

For fine-grained access control:

- **Implementation**: Custom security expressions and checks
- **Examples**:
  - Merchants can only access their own outlets
  - Users can only view orders for their assigned outlets
  - API keys have limited access based on configuration

### 7.3 Sensitive Data Protection

The platform implements comprehensive measures to protect sensitive data:

#### 7.3.1 Credit Card Information

- **Implementation**: Encryption and secure storage
- **Protection Measures**:
  - Card numbers and CVV are encrypted using RSA
  - Only the last four digits are stored in plain text
  - Card data is masked in logs and responses
  - PCI DSS compliance considerations

#### 7.3.2 API Credentials

- **Implementation**: Secure storage of third-party credentials
- **Protection Measures**:
  - Credentials are encrypted in the database
  - Access to credentials is restricted
  - Credentials are never exposed in logs or responses

#### 7.3.3 Personal Information

- **Implementation**: Secure handling of customer data
- **Protection Measures**:
  - Data minimization principles
  - Access controls for personal information
  - Logging restrictions for sensitive data

### 7.4 Request and Response Security

The platform implements security measures for HTTP communication:

#### 7.4.1 HTTPS Enforcement

- **Implementation**: HTTPS is enforced for all communications
- **Protection Measures**:
  - Redirect from HTTP to HTTPS
  - Strict Transport Security (HSTS)
  - Secure cookie settings

#### 7.4.2 CORS Configuration

- **Implementation**: Controlled Cross-Origin Resource Sharing
- **Protection Measures**:
  - Specific origin allowlisting
  - Credential handling configuration
  - Method and header restrictions

#### 7.4.3 Content Security Policy

- **Implementation**: CSP headers for content restriction
- **Protection Measures**:
  - Script source restrictions
  - Frame ancestors control
  - Resource loading policies

### 7.5 Logging and Auditing

The platform implements comprehensive logging for security monitoring:

#### 7.5.1 Security Event Logging

- **Implementation**: Dedicated logging for security events
- **Logged Events**:
  - Authentication attempts (successful and failed)
  - Authorization decisions
  - User management actions
  - Sensitive data access

#### 7.5.2 Request Logging

- **Implementation**: HTTP request and response logging
- **Logged Information**:
  - Request method, URI, and headers
  - Response status and timing
  - Payload logging with sensitive data masking
  - Custom headers for tracing

#### 7.5.3 Audit Trail

- **Implementation**: Event store for significant actions
- **Tracked Information**:
  - User ID for each action
  - Timestamp and event type
  - Affected entity and changes
  - Correlation IDs for request tracing

### 7.6 Error Handling and Security

The platform implements secure error handling:

#### 7.6.1 Exception Handling

- **Implementation**: Global exception handlers
- **Security Considerations**:
  - Sensitive information is removed from error messages
  - Different error responses for different environments
  - Consistent error structure for all responses

#### 7.6.2 Input Validation

- **Implementation**: Comprehensive validation framework
- **Protection Measures**:
  - Bean validation for request DTOs
  - Custom validators for complex rules
  - Sanitization of user input
  - Protection against injection attacks

### 7.7 Security in Asynchronous Processing

The platform ensures security in asynchronous operations:

#### 7.7.1 Message Queue Security

- **Implementation**: Secure message queue configuration
- **Protection Measures**:
  - Authentication for queue access
  - Encryption of sensitive message content
  - Access control for queue operations

#### 7.7.2 Security Context Propagation

- **Implementation**: Security context maintained across threads
- **Protection Measures**:
  - Context propagating task decorators
  - Consistent authorization in asynchronous tasks
  - Proper context cleanup after execution

### 7.8 Third-Party Integration Security

The platform implements security measures for external service integration:

#### 7.8.1 Outgoing Request Security

- **Implementation**: Secure HTTP client configuration
- **Protection Measures**:
  - Certificate validation
  - Connection timeouts
  - Retry with exponential backoff
  - Circuit breaking for failing services

#### 7.8.2 Webhook Security

- **Implementation**: Secure webhook execution
- **Protection Measures**:
  - HTTPS for all webhook URLs
  - Authentication for webhook endpoints
  - Payload signing for verification
  - Response validation and logging

### 7.9 Infrastructure Security

While not directly part of the codebase, the platform is designed with infrastructure security in mind:

#### 7.9.1 Containerization

- **Implementation**: Support for containerized deployment
- **Security Benefits**:
  - Isolation of components
  - Immutable infrastructure
  - Simplified security patching
  - Resource constraints

#### 7.9.2 Secrets Management

- **Implementation**: External secrets management support
- **Security Benefits**:
  - Separation of code and secrets
  - Centralized secret management
  - Secret rotation capabilities
  - Access control for secrets

The security and permissions framework of the AngularCRM payment orchestration platform provides a comprehensive approach to protecting sensitive data, controlling access, and monitoring security events. The combination of authentication mechanisms, authorization controls, sensitive data protection, and secure communication creates a robust security posture for the platform.

---

## 8. Conclusion

The AngularCRM payment orchestration platform is a sophisticated system designed to handle the complexities of payment processing, risk management, and customer management. Its modular architecture, comprehensive feature set, and robust security make it a powerful solution for businesses that need to process payments through multiple gateways while managing risk effectively.

Key strengths of the platform include:

1. **Modular Architecture**: The clear separation of concerns and well-defined interfaces make the system easy to maintain and extend.

2. **Flexible Integration**: The platform can integrate with multiple payment gateways, risk services, and other external systems, providing flexibility for different business needs.

3. **Comprehensive Risk Management**: The combination of preventive measures, risk alerts, and dispute handling provides robust protection against fraud and financial loss.

4. **Event-Driven Design**: The event-driven architecture ensures loose coupling between components and enables reactive processing of significant state changes.

5. **Secure Implementation**: The platform implements multiple layers of security to protect sensitive data and control access to system resources.

The documentation provided in this document offers a high-level overview of the platform's architecture, core functionalities, data models, integration points, and security measures. It serves as a starting point for understanding the system's design and implementation, but it should be complemented with more detailed documentation for specific components or features as needed.

As the platform continues to evolve, this documentation should be updated to reflect new features, architectural changes, and improved security measures. Regular reviews and updates will ensure that the documentation remains a valuable resource for developers, system architects, and other stakeholders.

---