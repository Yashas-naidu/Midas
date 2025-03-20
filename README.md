# Midas: Receiving, Validating, and Recording Financial Transactions

## Technologies Used:
- **Kafka**: Acts as a message queue to receive and process transactions asynchronously.
- **H2 Database**: A lightweight, in-memory SQL database for local development.
- **Spring Boot**: Implements Kafka consumers, database interactions, and REST APIs.
- **REST API**: Used to interact with the Incentive API and expose user balance queries.

---

## Transaction Flow:

### 1. **Receiving Transactions via Kafka**
- Transaction data is sent to Kafka.
- Kafka acts as a message queue, ensuring asynchronous transaction processing.
- A Kafka topic is defined in `application.yml`.
- Messages from the topic are consumed and converted into Java objects.

### 2. **Validating Transactions**
- The transaction validator ensures:
  - The `senderId` exists.
  - The `recipientId` exists.
  - The sender's balance is sufficient for the transaction.
- Entities:
  - **User**: Represents a user account.
  - **TransactionRecord**: Stores transaction details.
  - **Many-to-One Relationship**: Multiple transactions reference the same user.

### 3. **Recording Transactions**
- If validation passes, the transaction is recorded in the H2 database.
- Sender’s balance is deducted.
- Recipient’s balance is updated.

### 4. **Fetching Incentives via Incentive API**
- The transaction is sent to the **Incentive API** (`http://localhost:8080/incentive`) via a **POST request**.
- The API returns an incentive amount.
- The incentive is added **only to the recipient’s balance**.
- The transaction record is updated with the incentive.

### 5. **Exposing User Balance via REST API**
- A `/balance` endpoint is added to Midas Core.
- Users can query their balance via a **GET request**.
- If a user does not exist, the balance defaults to `0`.
- The REST API runs on port **33400**.

---

## **Decoupled System Architecture**
- Kafka handles message processing separately.
- The Incentive API operates independently.
- The balance-checking API is integrated but can be moved to a separate service later.

This design ensures **scalability, modularity, and resilience** in handling financial transactions.
