Here is the content of the `README.md` file in Markdown format:



# DESIGN PATTERNS:-
## A) Facade Design Pattern

The Facade Design Pattern is a structural design pattern that provides a simple interface to a complex subsystem.

Instead of interacting with multiple classes directly, the client interacts with one facade class, which internally manages the complexity.
## When to Use Facade Pattern

System has many complex classes

You want to simplify client interaction

You want to reduce dependencies between subsystems

## Real Life Example

Think of ordering food from a restaurant app.

**Without facade:** 

Customer
   ↓
Restaurant
Delivery Partner
Payment System
Notification Service

Customer must interact with many services.

**With facade:**

Customer
   ↓
FoodOrderFacade
   ↓
Restaurant + Delivery + Payment + Notification

Customer interacts with only one interface.

```
Step 1: Subsystem Classes
class CPU {
    void start() {
        System.out.println("CPU started");
    }
}

class Memory {
    void load() {
        System.out.println("Memory loaded");
    }
}

class HardDrive {
    void read() {
        System.out.println("Reading data from Hard Drive");
    }
}
Step 2: Facade Class
class ComputerFacade {

    private CPU cpu;
    private Memory memory;
    private HardDrive hardDrive;

    public ComputerFacade() {
        cpu = new CPU();
        memory = new Memory();
        hardDrive = new HardDrive();
    }

    public void startComputer() {
        cpu.start();
        memory.load();
        hardDrive.read();
    }
}
Step 3: Client Code
public class Main {

    public static void main(String[] args) {

        ComputerFacade computer = new ComputerFacade();
        computer.startComputer();
    }
}
```
**Output**

CPU started
Memory loaded
Reading data from Hard Drive

The client interacts only with ComputerFacade.

## Advantages

Reduces system complexity

Improves code readability

Loose coupling between client and subsystem

Simplifies API usage

## Disadvantages

Facade can become a god class if too many responsibilities are added.

**Real-world Examples in Java**

Common places where Facade pattern is used:

SLF4J logging framework

Spring Framework (JdbcTemplate)

Hibernate Session API

**Example in Spring:**

JdbcTemplate jdbcTemplate = new JdbcTemplate(dataSource);

JdbcTemplate acts as a facade for complex JDBC operations.

## When to Use Facade Pattern



System has many complex classes

You want to simplify client interaction

You want to reduce dependencies between subsystems


#### 🔑Service A never hard-codes Service B’s address.


Instead, it uses Service Discovery (often with a Load Balancer).

🧭 Option 1: Service Discovery (Most Common)
🔹 How it works

Service B instances register themselves with a Service Registry

Service A asks the registry:
👉 “Where is Service B?”

Registry returns one or more live instances

Request is sent to one of them

🗂 Popular Service Registries

Netflix Eureka

Consul

Apache Zookeeper

🔁 Two types of discovery
1️⃣ Client-Side Discovery

Service A decides which instance to call.

Service A → Service Registry → Service B instance


✔ Used by Netflix Eureka
❌ Client has more responsibility

2️⃣ Server-Side Discovery

Service A calls a load balancer, not the instance directly.

Service A → Load Balancer → Service B instance


✔ Cleaner for clients
✔ Common in cloud setups

☁️ Option 2: Kubernetes (Most Modern Way)

If you’re using Kubernetes, this problem is already solved.

🔹 How?

Each service gets a DNS name

Kubernetes does service discovery automatically

Example:

http://service-b.default.svc.cluster.local


Service A just calls:

http://service-b


Behind the scenes:

Kubernetes DNS

kube-proxy

Load balancing

✔ No Eureka needed
✔ Automatic scaling
✔ Industry standard today

🌐 Option 3: API Gateway (External Calls)

When external clients call your system:

Client → API Gateway → Service A → Service B


Common gateways:

Spring Cloud Gateway

Kong

Gateway handles:

Routing

Load balancing

Auth

Rate limiting

⚙️ Typical Spring Boot Setup (Real Projects)
Without Kubernetes
Spring Boot + Eureka + LoadBalancer + Feign

With Kubernetes (Recommended)
Spring Boot + Kubernetes Service + DNS














```markdown
# SystemDesign

✅ Step-by-Step OAuth 2.0 Login with Google

#### 🔧 0. Pre-Setup (One-Time by Quora Developer)

Before any user login happens, Quora registers their app with Google:

Quora creates an OAuth Client ID and Client Secret in the Google Cloud Console.

During this setup, Quora specifies:

App name, logo, privacy policy

Redirect URIs, e.g. https://quora.com/oauth/callback

Google stores all of this and issues:

client_id

client_secret

### 🧑‍💻 1. User Clicks "Login with Google" on Quora
You go to Quora, click:
👉 "Continue with Google"

Your browser is redirected to Google’s OAuth 2.0 authorization endpoint:

GET https://accounts.google.com/o/oauth2/v2/auth?
  client_id=QUORA_CLIENT_ID
  &redirect_uri=https://quora.com/oauth/callback
  &response_type=code
  &scope=email%20profile
  &state=xyz123

### 🔐 2. Google Shows Login Screen (if not already logged in)

You see Google’s login page (hosted on google.com) if you're not already logged in.

You enter your Google username and password.

This information never goes to Quora — only to Google.

### 🧾 3. Google Verifies and Authenticates

If your credentials are correct:

Google authenticates you.

Proceeds to ask for consent.

### 🔒 4. Consent Screen (Authorization)

If it's your first time logging in to Quora with Google:

Google shows a Consent Screen with:

App name (Quora)

Scopes requested (e.g., profile, email)

You click Allow to proceed.

### 📦 5. Google Redirects Back to Quora with Authorization Code

After you allow, Google redirects your browser to the redirect URI Quora registered:

https://quora.com/oauth/callback?code=abc123&state=xyz123
The code parameter is a temporary auth code

The state parameter is a CSRF protection measure (should match what Quora originally sent)

### 🔁 6. Quora Exchanges Code for Tokens (Server-to-Server)

Now, Quora’s backend server makes a secure POST request to Google’s token endpoint:

POST https://oauth2.googleapis.com/token
Content-Type: application/x-www-form-urlencoded

client_id=QUORA_CLIENT_ID
&client_secret=QUORA_CLIENT_SECRET
&code=abc123
&redirect_uri=https://quora.com/oauth/callback
&grant_type=authorization_code

### 📥 7. Google Responds with Tokens

Google responds with:

{
  "access_token": "ya29.a0AfH6SMABC123...",
  "expires_in": 3599,
  "refresh_token": "1//0fghijklmNOPQ...",
  "scope": "email profile",
  "token_type": "Bearer",
  "id_token": "eyJhbGciOiJSUzI1NiIsIn..."
}

Breakdown:
access_token: Short-lived token to access user info

id_token: JWT that identifies the user (optional in some flows)

refresh_token: Can be used to get new access tokens later (long-lived)

expires_in: Time in seconds (e.g., 3600s = 1 hour)

### 📤 8. Quora Uses Access Token to Get Your Profile

Quora now uses the access_token to call Google’s UserInfo API:

GET https://www.googleapis.com/oauth2/v3/userinfo
Authorization: Bearer ya29.a0AfH6SMABC123...
Google responds with your basic profile info:

{
  "sub": "1234567890",
  "name": "Priyanka Rauthan",
  "email": "priyanka@gmail.com",
  "picture": "https://lh3.googleusercontent.com/a/..."
}

### 🙋‍♀️ 9. Quora Logs You In

If this is your first time: Quora creates a new account with your Google info.

If you already have an account: Quora logs you in.

Your session is now active 🎉

### 🔄 10. (Optional) Later When Access Token Expires

When the access token expires (usually in 1 hour):

Quora sends the refresh_token to Google to get a new access token in the background — without user involvement.

POST https://oauth2.googleapis.com/token

client_id=...
&client_secret=...
&refresh_token=1//0fghijklmNOPQ...
&grant_type=refresh_token

# High-Level Architecture for a Payment Gateway

## 1. Overview

A Payment Gateway is a financial service that facilitates online transactions by securely transferring payment information between merchants, customers, banks, and payment processors. It ensures [...]

## 2. Key Features

Multi-Party Payment Processing (Customer, Merchant, Issuing Bank, Acquiring Bank, Payment Processor)

Security & Encryption (PCI-DSS Compliance, TLS, Tokenization)

Fraud Detection & Risk Management (AI-based anomaly detection, rule-based checks)

Multi-Currency & Cross-Border Transactions

Settlement & Reconciliation

Webhook Notifications & Callback Handling

Dispute & Chargeback Management

API Integrations for Merchants & Partners

## 3. High-Level Architecture

### 3.1 Client Side (Frontend & Merchant Integration)

Web, Mobile Apps, POS terminals integrate with Payment Gateway via APIs

Payment SDKs for different platforms (Android, iOS, Web)

User authentication via OAuth2 / OpenID Connect (OIDC)

### 3.2 Payment Gateway Backend Services

Transaction Processing Service

Handles user requests, initiates payments

Communicates with acquiring bank/payment processors

Ensures transactional integrity

Payment Orchestration Layer

Routes transactions based on cost, location, risk, and failure rate

Load-balancing across multiple acquiring banks/processors

Fraud Detection & Risk Engine

Uses ML-based fraud detection

Rule-based checks (velocity checks, blacklists, geo-blocking)

Tokenization Service

Stores sensitive card details securely using PCI DSS standards

Generates a token for merchants instead of raw card details

Notification & Webhook Service

Sends real-time transaction status updates to merchants

Supports webhooks, email, SMS notifications

Settlement & Reconciliation Service

Ensures funds are correctly settled between banks & merchants

Generates reports for financial auditing

Chargeback & Dispute Management

Allows merchants to manage disputed transactions

Works with banks for resolution

## 4. Infrastructure & Deployment

### 4.1 API Gateway & Load Balancer
Manages API rate limits, request authentication, and routing

Load balances traffic to backend services

### 4.2 Database Layer

Relational DB (PostgreSQL/MySQL) for transactions & settlements

NoSQL DB (MongoDB, Redis) for session management & caching

Data Lake (HDFS/S3) for storing historical transaction data

### 4.3 Security & Compliance

Encryption (AES-256, TLS 1.3) for secure data transmission

Tokenization & Vault Storage for sensitive card data

PCI-DSS, GDPR, ISO 27001 compliance

### 4.4 Event-Driven Architecture

Kafka for real-time event streaming (transaction events, notifications)

Elasticsearch + Kibana for log analysis and monitoring

### 4.5 Scalability & Reliability

Microservices-based architecture for scalability

Kubernetes (K8s) + Docker for containerized deployment

Auto-scaling based on traffic patterns

## 5. Workflow: Payment Processing Flow

Customer initiates a payment → Enters card details, selects UPI, or uses saved cards

Merchant Server calls Payment Gateway API → Sends encrypted transaction details

Payment Gateway validates & tokenizes card details → Checks for fraud & risk

Transaction sent to acquiring bank/payment processor → Bank verifies funds

Bank sends response (Success/Failure) → Payment Gateway notifies Merchant

Merchant fulfills order if successful → Updates customer via webhook

## 6. Tech Stack

Frontend: React.js, Angular, iOS, Android SDK

Backend: Java (Spring Boot) / Node.js (Express)

Database: PostgreSQL, Redis, MongoDB

Messaging & Event Streaming: Kafka, RabbitMQ

Security: OAuth2, JWT, PCI-DSS Compliance

Cloud & DevOps: AWS/GCP/Azure, Kubernetes, Docker
```

Let me know how else I can assist!
