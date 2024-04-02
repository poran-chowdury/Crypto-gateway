
# Crypto to Fiat Withdrawal System

This system enables users to withdraw money from cryptocurrency to fiat currency, transferring funds to different bank accounts in various countries.

![](./Crypto-to-Fiat%20Withdrawal%20System.drawio.png)
**System Design:**

The system will utilize a microservices architecture for scalability, resilience, and maintainability. Here are the key components:

* **User Service:** Handles user authentication, authorization, and management of user data (including KYC/AML information).
* **Wallet Service:** Interacts with the USDC blockchain to verify user balances and initiate USDC transfers.
* **Exchange Service:** Fetches real-time exchange rates and calculates the estimated BDT amount users will receive.
* **Fiat Processing Service:** Integrates with a licensed money transmitter partner to convert USDC to BDT and initiate transfers to recipient bank accounts.
* **Transaction Service:** Orchestrates the withdrawal process, interacts with other microservices, and updates transaction statuses.
* **Notification Service:** Sends notifications to users regarding transaction confirmations, updates, and potential delays.
* **Admin Service:** Provides functionalities for managing the system, including adding new features, monitoring system health, and handling regulatory compliance tasks.

**Process Flow:**

1. **User Login:** The user logs in to the system using their credentials (username/password or two-factor authentication).
2. **Withdrawal Initiation:** The user selects the "Withdraw" option, enters the desired USDC amount, and chooses the recipient bank account from their saved beneficiaries.
3. **Display Fees and Confirmation:** The system displays the estimated BDT amount considering the current exchange rate and withdrawal fees. The user reviews the details and confirms the transaction.
4. **Blockchain Interaction:** The Exchange  Service interacts with the USDC blockchain to verify the user's USDC balance and initiate the coin transfer. This involves creating and broadcasting a transaction on the blockchain.
5. **Fiat Processing:** The Transaction Service sends the withdrawal request to the Fiat Processing Service. This service interacts with a licensed money transmitter partner to convert the USDC to BDT and initiate the transfer to the recipient's bank account.
6. **Transaction Update:** The Transaction Service updates the transaction status and user interface to reflect the processing stage.
7. **Notifications:** The Notification Service sends updates to the user regarding transaction confirmations, estimated transfer times based on the recipient's location, and any potential delays encountered during the process.

**Additional Considerations:**

* **Security:** The system should implement robust security measures to protect user data and financial transactions. This includes encryption of sensitive data, secure login protocols, and regular security audits.
* **Compliance:**  The system should adhere to all Anti-Money Laundering (AML) and Know Your Customer (KYC) regulations to ensure responsible operation.
* **Error Handling and Rollback:** The system should be designed to handle potential errors gracefully. In case of blockchain transaction failures or fiat processing issues, the system should attempt retries or initiate rollback procedures to return USDC to the user's account.
* **Monitoring and Logging:** Comprehensive monitoring and logging mechanisms should be implemented to track system activity, identify potential issues, and ensure smooth operation.

**User Steps to Make a Transfer:**

1. **Login:** User logs in with their credentials (username/password or two-factor authentication).
2. **Withdrawal Initiation:** User selects "Withdraw" option, enters the USDC amount, selects the beneficiary bank account from saved beneficiaries, and reviews the estimated BDT amount displayed based on current exchange rates.
3. **Confirmation:** User confirms the transaction after reviewing details and fees (displayed upfront).

**Challenges and Handling:**

* **Blockchain Network Issues:** Transaction Service retries the USDC transfer on the blockchain if there are temporary network issues and notifies the user.
* **Fiat Processing Delays:** Users are informed upfront about estimated transfer times based on the transfer rule check. Notification Service sends updates if delays exceed expectations.
* **Insufficient Funds:** The system displays an error message if the user's USDC balance is insufficient.
* **Invalid Bank Account Details:** Integration with bank verification services minimizes the risk of invalid account details.

**Rollback Policy:**

* **Blockchain Failure:** If the USDC transfer fails on the blockchain due to a confirmed error, the transaction is reversed, and the user's USDC balance is restored.
* **Fiat Processing Failure:** The Fiat Processing Service attempts retries with the money transmitter partner. If unsuccessful after retries, the USDC is converted back and credited to the user's account.

**Security Measures:**

* **Spring Security:** Implement Spring Security framework for user authentication and authorization.
* **Secure Login:**  Enforce strong password hashing and two-factor authentication for logins.
* **Data Encryption:** Encrypt user data (including private keys) at rest and in transit using industry-standard algorithms (e.g., AES-256).
* **API Security:** Secure APIs with authentication (e.g., JWT tokens) and authorization mechanisms. Validate data received through APIs.
* **Regular Security Audits:** Conduct regular penetration testing and vulnerability assessments of the system.
* **Secure Communication:** Utilize HTTPS for all communication between users and the system.

**Suspicious Activity Handling:**

* **Login Attempts:** Limit login attempts and implement account lockouts after a certain number of failed attempts.
* **API Rate Limiting:** Implement rate limiting on API calls (e.g., Spring Security's RateLimiter) to prevent brute-force attacks and Denial-of-Service (DoS) attempts.
* **IP Monitoring:** Monitor user activity for suspicious patterns like unusual transaction volumes or geographical login anomalies. Flag such activities for manual review by security personnel.


This system design provides a foundation for a secure, efficient, and user-friendly platform for users to withdraw cryptocurrency and convert it to fiat currency.

-----------------


4. **Database:**
   A relational database management system (RDBMS) like PostgreSQL is a suitable choice for this system due to its: 
   - Scalability: Ability to handle large transaction volumes and user data growth.
   - Data Integrity: Features that ensure data accuracy and consistency

**Transfer Steps (Key Point 1):**

1. User logs in with MFA.
2. User initiates withdrawal, providing USDC amount and beneficiary details.
3. System validates user information and beneficiary details.
4. Withdrawal service gets exchange rate and calculates fees.
5. User confirms transaction details and fees.
6. System debits user's USDC wallet (connected via API).
7. System converts USDC to BDT using the exchange or liquidity pool.
8. Payment processor transfers BDT to recipient's bank account (considering country-specific limitations and fees).
9. System updates database and sends notifications to user about transaction status.

**Challenges and Handling (Key Point 2):**

* **Failed Transactions:**
  - System retries the transaction with the payment processor a predefined number of times.
  - If retries fail, the system rolls back the USDC debit and notifies the user with options to retry or contact support.
* **Insufficient Funds:**
  - System checks user's USDC balance before initiating the withdrawal.
  - If insufficient funds, the user receives an error message and cannot proceed.
* **Network Issues:**
  - System implements timeouts and retries for communication with external services (exchange, payment processor).
  - User receives notifications about delays due to network issues.
* **Non-compliant Transactions (KYC/AML):**
  - System automatically flags transactions that violate compliance rules.
  - Manual review and potential account suspension for suspicious activity.

**Rollback Policy (Key Point 3):**

- In case of failed transactions after retries, the system automatically reverses the USDC debit from the user's wallet.
- Users are notified about the rollback and the reason for failure.
- A log of rollback events is maintained for auditing purposes.

**Security (Key Point 4):**

- As mentioned in the system components, various security measures are implemented to protect user data, prevent unauthorized access, and ensure data integrity.

**Suspicious Activity Handling (Key Point 5):**

- **Wrong PIN Attempts:**
  - The system implements an account lockout policy after a certain number of consecutive failed login attempts.
  - Users can reset their passwords through a secure verification process.
- **Excessive API Calls:**
  - The API Gateway implements rate limiting to restrict the number of requests from a single device or IP within a specific timeframe.
  - This helps prevent denial-of-service attacks and malicious automation attempts.
- **Suspicious Transactions:**
  - The system analyzes transaction patterns and flags outliers based on pre-defined rules (e.g., large transfers, unusual destinations).
  - These transactions are flagged for manual review by the security team.


----------------
## API Documentation

### Authentication

#### POST /api/auth/login
- **Description:** Endpoint for user authentication.
- **Request Body:**
  ```json
  {
    "email": "user@example.com",
    "password": "123"
  }
  ```
- **Response:**
  ```json
  {
    "accessToken": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6IkpvaG4gRG9lIiwiaWF0IjoxNTE2MjM5MDIyfQ.SflKxwRJSMeKKF2QT4fwpMeJf36POk6yJV_adQssw5c",
    "tokenType": "Bearer",
    "expiresIn": 3600
  }
  ```
### Verify PIN
#### POST /api/auth/verify-pin
Description: Verify user PIN before processing a withdrawal.
Request Body:
```json
{
  "pin": "1234"
}
```
Authorization: Bearer Token
Response: 200 OK if PIN is correct, 401 Unauthorized if PIN is incorrect
### Beneficiary Management

#### POST /api/beneficiaries
- **Description:** Add a new beneficiary bank account.
- **Request Body:**
  ```json
  {
    "name": "John Doe",
    "country": "Bangladesh",
    "bankName": "Example Bank",
    "accountNumber": "123456789",
    "routingNumber": "BNGLBD123"
  }
  ```
- **Authorization:** Bearer Token
- **Response:** 201 Created

#### GET /api/beneficiaries
- **Description:** Retrieve all saved beneficiaries for the current user.
- **Authorization:** Bearer Token
- **Response:**
  ```json
  [
    {
      "id": 1,
      "name": "John Doe",
      "country": "Bangladesh",
      "bankName": "Example Bank",
      "accountNumber": "123456789",
      "routingNumber": "BNGLBD123"
    }
  ]
  ```

### Withdrawal Process

#### POST /api/withdrawals
- **Description:** Initiate a withdrawal request.
- **Request Body:**
  ```json
  {
  "amount": 100,
  "beneficiaryId": 1,
  "pin": "1234"
  }
  ```
- **Authorization:** Bearer Token
- **Response:** 202 Accepted


### Security Considerations
- All API endpoints require authentication using JWT tokens.
- Passwords are hashed and stored securely in the database.
- HTTPS is enforced for all API requests.
- Rate limiting is implemented to prevent abuse.



---
