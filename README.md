Abstract

This document specifies PayScript, a Groovy-based Domain-Specific Language (DSL) designed for payment programmability within banking systems. PayScript provides a powerful and flexible mechanism for banks to implement highly customised financial features and interact seamlessly with core banking systems and open-banking specifications.

 
Table of Contents

    1. Introduction 

1.1. What is PayScript? 

1.2. Problem Solved 

1.3. Audience 

1.4. Guiding Principles

    2. Conventions and Terminology

2.1. Keywords 

2.2. Data Type Notations 

2.3. PayScript-Specific Terms

    3. PayScript Core Concepts 

3.1. Groovy Foundation 

3.2. Script Execution Model 

3.3. Triggers and Parameters

    4. PayScript Language Specification 

4.1. Basic Syntax and Structure 

4.1.1. Comments 

4.1.2. Case Sensitivity 

4.1.3. "Hello, World!" Equivalent 

4.2. Data Types 

4.2.1. Groovy Primitive and Standard Types 

4.2.2. PayScript-Specific Data Types 

4.2.2.1. BalanceInfo 

4.2.2.2. PaymentInfo 

4.2.2.3. LockInfo 

4.2.2.4. AccountIdentifier and Related Types 

4.2.2.5. AmountInfo 

4.2.2.6. Enums (AccountIdentifierType, ConsentStatus, LockStatus, CurrencyEnum) 4.2.2.7. Other Domain-Specific Types (e.g., Reserve, ReserveInfo, UpdateReserveInfo, ReserveTransactionsFilter, ReserveTransaction) 

4.3. Variables and Constants 

4.4. Operators 

4.5. Control Flow 

4.6. Functions and Procedures 

4.7. Error Handling

    5. PayScript Standard Library (Built-in Functions) 

5.1. Account Information Functions 

5.1.1. accountExists 

5.1.2. checkAccountFunds 

5.1.3. getBalance 

5.1.4. getRelatedAccountIds 

5.1.5. getConsent 

5.2. Payment Functions 

5.2.1. createPayment 

5.2.2. requestPayment 

5.2.3. getPaymentInfo 

5.3. Hard Lock Functions 

5.3.1. getLockStatus 

5.3.2. getLockInfo 

5.3.3. createLock 

5.3.4. getAccountLocks 

5.3.5. releaseLock 

5.4. Reserve Functions 

5.4.1. createReserve 

5.4.2. editReserve 

5.4.3. getAccountReserves 

5.4.4. getReserveInfo 

5.4.5. addFundsToReserve 

5.4.6. moveFundsBetweenReserves 

5.4.7. withdrawFundsFromReserve 

5.4.8. getReserveTransactions 

5.4.9. getReserveTransactionsCount 

5.4.10. deleteReserve 

5.5. Utility Functions 

5.5.1. callScript

5.5.2 logMessage

    6. Security and Compliance 

6.1. Data Handling 

6.2. Transactional Integrity 

6.3. Auditing Considerations

    7. Deployment and Execution Environment 

7.1. PayScript Interpreter 

7.2. Quant Flow Sandbox 

7.3. Quant Flow Product Integration

    Examples (as provided) 

8.1. Basic Payment Transfer

    8. Conformance

 
# 1. Introduction

## 1.1. What is PayScript?

PayScript is a Domain-Specific Language (DSL) built on Groovy, specifically engineered for the programmable automation of financial transactions and interactions within banking systems. It allows developers to define complex payment logic, integrate with core banking functionalities, and adhere to open-banking specifications through concise and expressive scripts.

## 1.2. Problem Solved

Traditional banking systems often struggle with the rapid and flexible implementation of highly customised financial products and services. PayScript addresses this by providing a high-level, yet powerful, scripting environment that abstracts away the complexities of direct core banking system interactions. By interfacing with core banking systems and open-banking specifications, PayScript empowers banks to offer highly specific features with significant customisation, enabling faster innovation and adaptation to market demands.

## 1.3. Audience

The primary audience for PayScript is developers employed by financial institutions. These developers will utilise PayScript to write, deploy, and manage scripts that automate and customise various banking operations, ranging from simple fund transfers to sophisticated financial product logic.

## 1.4. Guiding Principles

PayScript's design is guided by the following core principles:

    Simplicity: Providing an intuitive and readable syntax that leverages the familiarity of Groovy, allowing for rapid development and easier maintenance of financial logic.

    Interoperability: Designed to seamlessly integrate with existing core banking systems and adhere to industry-standard open-banking specifications, ensuring broad applicability and connectivity.

# 2. Conventions and Terminology

## 2.1. Keywords

Keywords from the Groovy language retain their standard meaning. PayScript introduces specific built-in function names that act as high-level commands.

## 2.2. Data Type Notations

    Type: Refers to a specific PayScript or Groovy data type.

    List<Type>: Represents a list (or array) of elements of Type.

    Map<KeyType, ValueType>: Represents a map (or dictionary) where keys are KeyType and values are ValueType.

    Enum: Denotes an enumeration type with a predefined set of values.

# 3. PayScript Core Concepts

## 3.1. Groovy Foundation

PayScript is a DSL built on Apache Groovy. This means that all valid Groovy syntax, including its flexible typing, object-oriented features, closures, and extensive standard library, is available within PayScript. Developers familiar with Groovy will find PayScript's core programming constructs (variables, operators, control flow, etc.) immediately recognisable.

## 3.2. Script Execution Model

PayScript scripts are interpreted. They are executed within a dedicated PayScript interpreter environment. This interpreter handles the parsing of the script, execution of its logic, and interaction with underlying banking systems via the built-in financial functions.

## 3.3. Triggers and Parameters

PayScript scripts are designed to be executed as part of an "Automation" within the Quant Flow ecosystem. When an automation is created, a trigger mechanism is defined, specifying when the script should run.

Scripts can also be configured to accept parameters upon execution. These parameters are defined within the script using a specific placeholder syntax and are provided when the automation is created or triggered.

Available Parameter Types:

PayScript supports the following parameter types for triggers:

`BOOL`

`BOOL_ARRAY`

`INT`

`INT_ARRAY`

`DECIMAL`

`DECIMAL_ARRAY`

`STRING`

`STRING_ARRAY`

`SCAN` (for `AccountIdentifierType.SCAN`)

`IBAN` (for `AccountIdentifierType.IBAN`)

`PAN` (for `AccountIdentifierType.PAN`)

`BICSWIFT` (for `AccountIdentifierType.BICSWIFT`)

Optional Parameters:

Parameters can be made optional by appending a question mark (?) to their type definition. If an optional parameter is not provided during automation creation or execution, its value will be null within the script.

 

Example of Optional Parameter Declaration:
```groovy
def stringVar = ${stringVar:string?}; // This parameter is optional
```
 

Example of Trigger and Parameter Declaration:
```groovy
trigger = "on_demand"; // Defines the trigger for this script

def payer  = ${payer:scan}; // Defines a parameter named 'payer' of type AccountIdentifier (SCAN)
def payee = ${payee:scan}; // Defines a parameter named 'payee' of type AccountIdentifier (SCAN)
def amount = ${amount:decimal}; // Defines a parameter named 'amount' of type BigDecimal
```
 
# 4. PayScript Language Specification

## 4.1. Basic Syntax and Structure

As PayScript is a Groovy-based DSL, its basic syntax and structure largely adhere to Groovy conventions.

### 4.1.1. Comments

Comments in PayScript follow Groovy's conventions:

- Single-line comments: Start with //

```groovy
// This is a single-line comment
def myVar = 10;
```

- Multi-line comments: Enclosed between /* and */
```groovy
/*
 * This is a multi-line comment.
 * It can span multiple lines.
 */
def anotherVar = "hello";
```

### 4.1.2. Case Sensitivity

PayScript, inheriting from Groovy, is case-sensitive. myVariable is distinct from myvariable.

### 4.1.3. "Hello, World!" Equivalent

While a traditional "Hello, World!" might just print text, PayScript's core purpose revolves around financial operations. A more representative "Hello, World!" equivalent in PayScript demonstrates a basic financial action, such as initiating a payment.

```groovy
// Define the trigger for this script
trigger = "on_demand";

// Declare parameters that will be provided when the automation is created
def payer  = ${payer:scan}; // e.g., "10-20-30", "12345678"
def payee = ${payee:scan};  // e.g., "40-50-60", "87654321"
def amount = ${amount:decimal}; // e.g., 100.50

// Construct a PaymentInfo object
def payment = PaymentInfo.builder()
        .payer(AccountInfo.builder()
                .type(AccountIdentifierType.SCAN)
                .identifier(payer)
                .build())
        .payee(AccountInfo.builder()
                .type(AccountIdentifierType.SCAN)
                .identifier(payee)
                .build())
        .amountInfo(AmountInfo.builder()
                .currency(CurrencyEnum.GBP) // Assuming GBP for this example
                .amount(amount)
                .build())
        .purpose("Automated initial transfer") // A purpose for the payment
        .paymentReference("PS-HW-001") // A unique reference
        .build();

// Call the built-in function to create the payment
def newPaymentId = createPayment(payment);

// The script implicitly completes here. The result of createPayment (newPaymentId)
// can be used or logged by the Quant Flow system, though direct console output isn't typical.
```

This example illustrates the core flow: defining input parameters, constructing a financial object (PaymentInfo), and invoking a built-in PayScript function (createPayment) to perform a financial operation.

## 4.2. Data Types

PayScript extends Groovy's rich set of data types with specific types tailored for financial operations.

### 4.2.1. Groovy Primitive and Standard Types

All standard Groovy data types are available, including but not limited to:

- String: Text sequences.

- Integer, Long: Whole numbers.

- BigDecimal: Arbitrary-precision decimal numbers, crucial for financial calculations where precision is paramount.

- Boolean: true or false.

- List: Ordered collections of objects.

- Map: Key-value pairs.

- UUID: Universally Unique Identifiers.

- Instant: A point in time (from java.time package).

#### 4.2.2.1. `BalanceInfo`

Represents the balance details for an account.

- `BigDecimal totalBalance`: The total sum of funds in the account.

- `BigDecimal availableBalance`: The portion of the total balance that is currently available for use (i.e., not held, locked, or otherwise restricted).

Example:
```groovy
def myBalance = getBalance(someAccountIdentifier);
def total = myBalance.totalBalance;
def available = myBalance.availableBalance;
```

#### 4.2.2.2. `PaymentInfo`

Encapsulates all necessary details for a payment transaction.

- UUID id: Unique identifier for the payment.

- AccountInfo payer: Details of the account initiating the payment.

- AccountInfo payee: Details of the account receiving the payment.

- AmountInfo amount: The currency and amount of the payment.

- String purpose: A description of the payment's purpose.

- PaymentStatus status: The current status of the payment (e.g., PENDING, COMPLETED, FAILED).

- Instant createdAt: Timestamp when the payment information was created.

- Instant updatedAt: Timestamp of the last update to the payment information.

Builder Pattern Example for PaymentInfo:
```groovy
def payment = PaymentInfo.builder()
        .payer(AccountInfo.builder() /* ... / .build())
        .payee(AccountInfo.builder() / ... / .build())
        .amountInfo(AmountInfo.builder() / ... */ .build())
        .purpose("Monthly Subscription")
        .paymentReference("SUB-2024-05")
        .build();
```

#### 4.2.2.3. `LockInfo`

Details about a "hard lock" on funds.

- `UUID id`: Unique identifier for the lock.

- `Instant createdAt`: Timestamp when the lock was created.

- `AmountInfo amount`: The amount of money locked.

- `AccountInfo fromAccount`: The account from which funds are locked.

- `AccountInfo toAccount`: The destination account for the locked funds (if applicable, e.g., for escrow).

- `Instant expiryDate`: Date and time when the lock is scheduled to expire.

- `String paymentReference`: A reference associated with the lock, often linking to a payment.

- `LockStatus status`: The current status of the lock.

Builder Pattern Example for LockInfo:
```groovy
def lock = LockInfo.builder()
        .amount(AmountInfo.builder().currency(CurrencyEnum.GBP).amount(100.00G).build())
        .fromAccount(AccountInfo.builder().type(AccountIdentifierType.SCAN).identifier("1234567890").build())
        .expiryDate(Instant.now().plus(7, ChronoUnit.DAYS))
        .paymentReference("Order-7890")
        .build();
```


#### 4.2.2.4. AccountIdentifier and Related Types

These types define various ways to identify a financial account. AccountIdentifier is a union or sealed type that can be one of several specific identification methods.

AccountIdentifier:

- `SCAN`: `{String sortCode, String accountNumber}` (UK-specific)

- `PAN`: `{String pan}` (Primary Account Number, typically for cards)

- `IBAN`: `{String iban}` (International Bank Account Number)

- `BicSwift`: `{String bic, String swift}` (SWIFT/BIC code)

- `RelatedAccountInfo`: Provides context for accounts related to a specified account.

- `AccountIdentifier accountIdentifier`: The identifier of the related account.

- `AccountIdentifierType type`: The type of identifier used for the related account.

#### 4.2.2.5. AmountInfo

Represents a monetary value with its associated currency.

- `CurrencyEnum currency`: The currency of the amount (e.g., GBP, USD, EUR).

- `BigDecimal amount`: The monetary value, using BigDecimal for precision.

Example:
```groovy
def paymentAmount = AmountInfo.builder()
        .currency(CurrencyEnum.USD)
        .amount(new BigDecimal("250.75"))
        .build();
```

#### 4.2.2.6. Enums

PayScript defines several enumeration types for clarity and type safety.

- `AccountIdentifierType`: `{ SCAN, IBAN, PAN, BICSWIFT }`

- `ConsentStatus`: `{ GIVEN, NOT_GIVEN, REQUESTED }`

- `LockStatus`: `{ ACTIVE, PROCESSING, RELEASE_FAILED }`

`CurrencyEnum`: { GBP, USD, EUR, JPY, ... }.

#### 4.2.2.7. Other Domain-Specific Types

The following types are also part of the PayScript domain model, typically used when interacting with Reserve functions:

`Reserve`: Represents the structure for creating a new reserve. It contains attributes like accountIdentifier, name, priority, savingGoal, description, reserveId (for new reserves, this would be generated by the system).

`UpdateReserveInfo`: Used to update properties of an existing reserve.

`ReserveInfo`: Provides detailed information about an existing reserve, including createdAt, updatedAt, balance, etc.

`ReserveTransactionsFilter`: Criteria for filtering reserve transactions (e.g., date ranges, types).

`ReserveTransaction`: Details of a specific transaction within a reserve (e.g., fund addition, withdrawal, movement).

## 4.3. Variables and Constants

Variables in PayScript are declared using the Groovy def keyword or by explicit type declaration. They are mutable by default. Constants are typically defined as final variables.

Examples:
```groovy
def myAmount = 100.50G; // BigDecimal literal
String paymentRef = "INV-001";
final String BASE_CURRENCY = "GBP";
```

## 4.4. Operators

All standard Groovy operators are available, including:

- Arithmetic Operators: +, -, *, /, %

- Comparison Operators: ==, !=, <, <=, >, >=

- Logical Operators: && (and), || (or), ! (not)

- Assignment Operators: =, +=, -=, etc.

- Elvis Operator: ?: (for null-safe assignments)

```groovy
if (checkAccountFunds(payerAccount, requestedAmount)) {
    // Funds available, proceed
} else {
    // Insufficient funds
}
```


Looping Constructs: 

`for` loops (including C-style, enhanced for-each, and ranges)

`while` loops

`do-while` loops

```groovy
List<LockInfo> locks = getAccountLocks(myAccount);
for (lock in locks) {
    println("Lock ID: ${lock.id}, Amount: ${lock.amount.amount}");
}
```

## 4.6. Functions and Procedures

PayScript allows developers to define custom Groovy functions and closures. These can be used to encapsulate reusable logic within a script. The primary interaction with the banking system, however, occurs through the built-in PayScript functions (detailed in Section 5).

Example of a custom function:
```groovy
def logPaymentResult(paymentId, status) {
    println "Payment with ID ${paymentId} completed with status: ${status}";
    // In a real scenario, this might log to an audit trail or external system
}

// ... later in the script
// logPaymentResult(newPaymentId, getPaymentInfo(newPaymentId).status);
```

## 4.7. Error Handling

PayScript currently relies on Groovy's standard exception handling mechanism. When an error occurs during script execution (e.g., invalid input, system unavailability, or a failed financial operation), a Groovy exception will be thrown.

Developers are responsible for using try-catch-finally blocks to gracefully handle potential errors within their scripts. Uncaught exceptions will typically result in the termination of the script execution and an error state reported by the Quant Flow platform.

Example of basic error handling:
```groovy
try {
    def newPaymentId = createPayment(payment);
    // Further processing if payment successful
} catch (Exception e) {
    // Log the error, notify an administrator, or attempt a rollback
    println "Payment failed: ${e.message}";
    // Potentially call a utility to revert state or log incident
}
```

# 5. PayScript Standard Library (Built-in Functions)

The power of PayScript lies in its rich set of built-in functions designed to interact with core banking functionalities. These functions abstract the underlying API calls and provide a high-level, type-safe interface for common financial operations.

## 5.1. Account Information Functions

These functions retrieve information about accounts and their associated properties.

### 5.1.1. accountExists

Checks whether an account exists in the system.

    Description: Verifies the existence of an account based on its identifier.

    Input Parameters:

        accountIdentifier: AccountIdentifier (the identifier of the account to check).

    Output Parameters:

        Boolean: true if the account exists, false otherwise.

Example:
```groovy
def myAccount = AccountIdentifier.builder().type(AccountIdentifierType.SCAN).sortCode("10-20-30").accountNumber("12345678").build();
if (accountExists(myAccount)) {
    println "Account exists!";
} else {
    println "Account does not exist.";
}
```

### 5.1.2. checkAccountFunds

Checks whether an account has the required funds available for a transaction.

    Description: Determines if an account's available balance is sufficient for a specified amount.

    Input Parameters:

        accountIdentifier: AccountIdentifier (the account to check funds for).

        amountInfo: AmountInfo (the amount to check availability for).

    Output Parameters:

        Boolean: true if sufficient funds are available, false otherwise.

Example:
```groovy
def accountToCheck = AccountIdentifier.builder().type(AccountIdentifierType.IBAN).iban("GB29NWBK60161331926819").build();
def amountNeeded = AmountInfo.builder().currency(CurrencyEnum.GBP).amount(500.00G).build();
if (checkAccountFunds(accountToCheck, amountNeeded)) {
    println "Funds are sufficient for the transaction.";
} else {
    println "Insufficient funds.";
}
```

### 5.1.3. getBalance

Retrieves the total and available balance for a specified account.

    Description: Provides a BalanceInfo object containing the total and available funds for an account.

    Input Parameters:

        accountIdentifier: AccountIdentifier (the account for which to get the balance).

    Output Parameters:

        BalanceInfo: An object containing totalBalance and availableBalance.

Example:
```groovy
def account = AccountIdentifier.builder().type(AccountIdentifierType.SCAN).sortCode("11-22-33").accountNumber("98765432").build();
def balance = getBalance(account);
println "Total Balance: ${balance.totalBalance} / Available Balance: ${balance.availableBalance}";
```

### 5.1.4. getRelatedAccountIds

Retrieves a list of accounts related to a specified account.

    Description: Returns a list of RelatedAccountInfo objects, indicating accounts linked or associated with the primary account.

    Input Parameters:

        accountIdentifier: AccountIdentifier (the primary account to query for related accounts).

    Output Parameters:

        List<RelatedAccountInfo>: A list of related account identifiers and their types.

Example:
```groovy
def primaryAccount = AccountIdentifier.builder().type(AccountIdentifierType.IBAN).iban("DE89370400440532013000").build();
def relatedAccounts = getRelatedAccountIds(primaryAccount);
relatedAccounts.each { relAccount ->
    println "Related Account: ${relAccount.accountIdentifier.type} - ${relAccount.accountIdentifier.identifier}";
}
```

### 5.1.5. getConsent

Retrieves the consent status for a particular account in relation to another.

    Description: Used to check if a giverAccountId has granted ConsentType to a holderAccountId.

    Input Parameters:

        giverAccountId: AccountIdentifier (the account that grants consent).

        holderAccountId: AccountIdentifier (the account that holds the consent).

        type: ConsentType (the specific type of consent being queried, e.g., PAYMENT_INITIATION, ACCOUNT_INFORMATION). (Note: ConsentType was inferred, please provide its definition if different)

    Output Parameters:

        ConsentStatus: The status of the consent (GIVEN, NOT_GIVEN, REQUESTED).

Example:
```groovy
def giver = AccountIdentifier.builder().type(AccountIdentifierType.IBAN).iban("NL91ABNA0417164300").build();
def holder = AccountIdentifier.builder().type(AccountIdentifierType.SCAN).sortCode("10-20-30").accountNumber("12345678").build();
def status = getConsent(giver, holder, ConsentType.PAYMENT_INITIATION);
println "Consent Status: ${status}";
```
 
## 5.2. Payment Functions

These functions facilitate various types of payment operations.

### 5.2.1. createPayment

Initiates a payment transaction between two accounts.

    Description: Executes a payment instruction based on the provided PaymentInfo.

    Input Parameters:

        paymentInfo: PaymentInfo (an object containing all payment details).

    Output Parameters:

        String: The ID of the created payment.

Example:
```groovy
def payerScan = AccountIdentifier.builder().type(AccountIdentifierType.SCAN).sortCode("10-20-30").accountNumber("12345678").build();
def payeeScan = AccountIdentifier.builder().type(AccountIdentifierType.SCAN).sortCode("40-50-60").accountNumber("87654321").build();

def paymentAmount = AmountInfo.builder().currency(CurrencyEnum.GBP).amount(100.50G).build();

def paymentDetails = PaymentInfo.builder()
        .payer(AccountInfo.builder().identifier(payerScan).build()) // Assuming AccountInfo can be built from AccountIdentifier
        .payee(AccountInfo.builder().identifier(payeeScan).build())
        .amountInfo(paymentAmount)
        .purpose("Online Purchase")
        .paymentReference("ORD-12345")
        .build();

def paymentId = createPayment(paymentDetails);
println "Payment created with ID: ${paymentId}";
```

### 5.2.2. requestPayment

Sends a payment request from one account to another.

    Description: Initiates a request for a payment from a payer to a payee. This does not immediately transfer funds but rather sets up a request that the payer needs to approve.

    Input Parameters:

        paymentInfo: PaymentInfo (details of the payment request).

    Output Parameters:

        String: The ID of the payment request.

Example:
```groovy
def requesterAccount = AccountIdentifier.builder().type(AccountIdentifierType.IBAN).iban("GB29NWBK60161331926819").build();
def targetAccount = AccountIdentifier.builder().type(AccountIdentifierType.SCAN).sortCode("10-20-30").accountNumber("12345678").build();

def requestedAmount = AmountInfo.builder().currency(CurrencyEnum.USD).amount(75.00G).build();

def paymentRequest = PaymentInfo.builder()
        .payer(AccountInfo.builder().identifier(targetAccount).build()) // Target is the payer in this context
        .payee(AccountInfo.builder().identifier(requesterAccount).build()) // Requester is the payee
        .amountInfo(requestedAmount)
        .purpose("Invoice Payment")
        .build();

def requestId = requestPayment(paymentRequest);
println "Payment request sent with ID: ${requestId}";
```

### 5.2.3. getPaymentInfo

Retrieves information about a specific payment.

    Description: Fetches the details of a previously initiated payment or payment request.

    Input Parameters:

        paymentId: String (the ID of the payment to retrieve).

    Output Parameters:

        PaymentInfo: An object containing all details of the payment.

Example:
```groovy
def retrievedPayment = getPaymentInfo("some-payment-uuid-123");
if (retrievedPayment) {
    println "Payment ID: ${retrievedPayment.id}, Status: ${retrievedPayment.status}, Amount: ${retrievedPayment.amount.amount}";
} else {
    println "Payment not found.";
}
```

## 5.3. Hard Lock Functions

These functions manage "hard locks," making funds unavailable to a customer.

### 5.3.1. getLockStatus

Shows the status of a specific lock.

    Description: Retrieves the current status of a hard lock.

    Input Parameters:

        lockId: String (the ID of the lock).

    Output Parameters:

        LockStatus: The status of the lock (ACTIVE, PROCESSING, RELEASE_FAILED).

Example:
```groovy
def currentLockStatus = getLockStatus("some-lock-uuid-456");
println "Lock Status: ${currentLockStatus}";
```

### 5.3.2. getLockInfo

Returns comprehensive information about a lock.

    Description: Provides a LockInfo object with all details pertaining to a specific lock.

    Input Parameters:

        lockId: String (the ID of the lock).

    Output Parameters:

        LockInfo: An object containing full lock details.

Example:
```groovy
def lockDetails = getLockInfo("some-lock-uuid-456");
if (lockDetails) {
    println "Locked Amount: ${lockDetails.amount.amount} from Account: ${lockDetails.fromAccount.identifier}";
}
```
 
### 5.3.3. createLock

Creates a hard lock, making money unavailable to the customer.

    Description: Establishes a hard lock on funds, potentially involving one or two parties (e.g., for escrow).

    Input Parameters:

        lockInfo: LockInfo (the details for the new lock).

    Output Parameters:

        String: The ID of the newly created lock.

Example:
```groovy
import java.time.Instant
import java.time.temporal.ChronoUnit

def payerAccount = AccountIdentifier.builder().type(AccountIdentifierType.SCAN).sortCode("10-20-30").accountNumber("12345678").build();

def newLock = LockInfo.builder()
        .amount(AmountInfo.builder().currency(CurrencyEnum.GBP).amount(250.00G).build())
        .fromAccount(AccountInfo.builder().identifier(payerAccount).build())
        .expiryDate(Instant.now().plus(30, ChronoUnit.DAYS))
        .paymentReference("Escrow-For-Purchase-XYZ")
        .build();

def newLockId = createLock(newLock);
println "New Lock created with ID: ${newLockId}";
```
 
### 5.3.4. getAccountLocks

Gets a list of all active locks on a specific account.

    Description: Retrieves a list of all LockInfo objects associated with a given account.

    Input Parameters:

        accountIdentifier: AccountIdentifier (the account for which to retrieve locks).

    Output Parameters:

        List<LockInfo>: A list of all locks on the account.

Example:
```groovy
def accountWithLocks = AccountIdentifier.builder().type(AccountIdentifierType.IBAN).iban("DE89370400440532013000").build();
def locksOnAccount = getAccountLocks(accountWithLocks);
locksOnAccount.each { lock ->
    println "Found Lock: ${lock.id} for amount ${lock.amount.amount}";
}
```

### 5.3.5. releaseLock

Releases a hard lock, making the locked amount available or transferring it to a beneficiary.

    Description: Terminates a hard lock, either by returning the funds to the fromAccount or by transferring them to the toAccount specified in the LockInfo.

    Input Parameters:

        lockId: String (the ID of the lock to release).

    Output Parameters:

        void: This function does not return a value but performs an action.

Example:
```groovy
// Assuming 'myActiveLockId' is the ID of an active lock
releaseLock("myActiveLockId");
println "Lock 'myActiveLockId' has been released.";
```

## 5.4. Reserve Functions

These functions manage "reserves" which are virtual sub-accounts or goals within a main account.

### 5.4.1. createReserve

Creates a new reserve for an account.

    Description: Establishes a new designated reserve (e.g., a "savings goal" or "travel fund") linked to a primary account.

    Input Parameters:

        reserve: Reserve (details for the new reserve, including name, goal, and associated account).

    Output Parameters:

        UUID: The unique ID of the newly created reserve.

Example:
```groovy
def accountForReserve = AccountIdentifier.builder().type(AccountIdentifierType.SCAN).sortCode("10-20-30").accountNumber("12345678").build();
def newSavingGoal = Reserve.builder()
        .accountIdentifier(accountForReserve)
        .name("Holiday Fund")
        .savingGoal(new BigDecimal("1500.00"))
        .priority(1)
        .description("Funds for upcoming holiday")
        .build();

def reserveId = createReserve(newSavingGoal);
println "Created new reserve with ID: ${reserveId}";
```

### 5.4.2. editReserve

Updates properties of an existing reserve.

    Description: Modifies details of a specific reserve, such as its name, priority, or saving goal.

    Input Parameters:

        reserveInfo: UpdateReserveInfo (an object containing the reserve ID and the fields to update).

    Output Parameters:

        UUID: The ID of the updated reserve.

Example:
```groovy
def reserveToUpdateId = "some-reserve-uuid-789";
def updatedReserveDetails = UpdateReserveInfo.builder()
        .reserveId(UUID.fromString(reserveToUpdateId))
        .name("Updated Holiday Fund (Paris Trip)")
        .savingGoal(new BigDecimal("2000.00"))
        .build();

def updatedId = editReserve(updatedReserveDetails);
println "Reserve ${updatedId} updated.";
```
 
### 5.4.3. getAccountReserves

Gets the list of all reserves associated with a specific account.

    Description: Retrieves a list of all ReserveInfo objects linked to a given primary account.

    Input Parameters:

        accountIdentifier: AccountIdentifier (the account for which to retrieve reserves).

    Output Parameters:

        List<ReserveInfo>: A list of all reserves on the account.

Example:
```groovy
def accountWithReserves = AccountIdentifier.builder().type(AccountIdentifierType.IBAN).iban("NL91ABNA0417164300").build();
def reserves = getAccountReserves(accountWithReserves);
reserves.each { res ->
    println "Reserve: ${res.name}, Balance: ${res.balance}, Goal: ${res.savingGoal}";
}
```
 
### 5.4.4. getReserveInfo

Retrieves detailed information about a specific reserve.

    Description: Provides a ReserveInfo object with comprehensive details about a particular reserve.

    Input Parameters:

        reserveId: String (the ID of the reserve).

    Output Parameters:

        ReserveInfo: An object containing full reserve details.

Example:
```groovy
def detailedReserve = getReserveInfo("some-reserve-uuid-789");
if (detailedReserve) {
    println "Reserve Name: ${detailedReserve.name}, Current Balance: ${detailedReserve.balance}";
}
```
 
### 5.4.5. addFundsToReserve

Adds funds from a user's main account to a reserve.

    Description: Transfers a specified amount from the main fromAccount into the designated toReserve.

    Input Parameters:

        fromAccount: AccountIdentifier (the main account to draw funds from).

        toReserve: String (the ID of the target reserve).

        amount: BigDecimal (the amount to add).

        reference: String (a description or reference for the transaction).

    Output Parameters:

        UUID: The transaction ID for this reserve operation.

Example:
```groovy
def myMainAccount = AccountIdentifier.builder().type(AccountIdentifierType.SCAN).sortCode("10-20-30").accountNumber("12345678").build();
def holidayReserveId = "some-holiday-reserve-uuid";
def transferAmount = new BigDecimal("50.00");
def transactionId = addFundsToReserve(myMainAccount, holidayReserveId, transferAmount, "Weekly savings contribution");
println "Added funds to reserve, Transaction ID: ${transactionId}";
```
 
### 5.4.6. moveFundsBetweenReserves

Moves funds from one reserve to another.

    Description: Transfers a specified amount directly between two existing reserves belonging to the same user/account.

    Input Parameters:

        fromReserve: String (the ID of the source reserve).

        toReserve: String (the ID of the destination reserve).

        amount: BigDecimal (the amount to move).

        reference: String (a description or reference for the transaction).

    Output Parameters:

        UUID: The transaction ID for this reserve operation.

Example:
```groovy
def travelReserve = "travel-fund-uuid";
def carRepairReserve = "car-repair-fund-uuid";
def moveAmount = new BigDecimal("100.00");
def moveTxnId = moveFundsBetweenReserves(travelReserve, carRepairReserve, moveAmount, "Emergency car repair");
println "Moved funds between reserves, Transaction ID: ${moveTxnId}";
```
 
### 5.4.7. withdrawFundsFromReserve

Moves funds from a user's reserve back to their main account.

    Description: Transfers a specified amount from a reserve back into the user's primary accountIdentifier.

    Input Parameters:

        fromReserve: String (the ID of the source reserve).

        accountIdentifier: AccountIdentifier (the main account to transfer funds to).

        amount: BigDecimal (the amount to withdraw).

        reference: String (a description or reference for the transaction).

    Output Parameters:

        UUID: The transaction ID for this reserve operation.

Example:
```groovy
def shoppingReserve = "shopping-fund-uuid";
def myCheckingAccount = AccountIdentifier.builder().type(AccountIdentifierType.SCAN).sortCode("10-20-30").accountNumber("12345678").build();
def withdrawAmount = new BigDecimal("75.00");
def withdrawTxnId = withdrawFundsFromReserve(shoppingReserve, myCheckingAccount, withdrawAmount, "Weekend shopping");
println "Withdrew funds from reserve, Transaction ID: ${withdrawTxnId}";
```

### 5.4.8. getReserveTransactions

Retrieves a list of transactions for a specific reserve.

    Description: Fetches historical transactions associated with a reserve, with options for filtering and pagination.

    Input Parameters:

        reserveId: String (the ID of the reserve).

        filter: ReserveTransactionsFilter (an object defining criteria like date range, transaction type, etc.).

        offset: Integer (for pagination, the starting index of results).

        limit: Integer (for pagination, the maximum number of results to return).

    Output Parameters:

        List<ReserveTransaction>: A list of reserve transactions, ordered by date (latest first).

Example:
```groovy
import java.time.Instant
import java.time.temporal.ChronoUnit

def myReserve = "some-reserve-uuid";
def filter = ReserveTransactionsFilter.builder()
        .fromDate(Instant.now().minus(30, ChronoUnit.DAYS))
        .toDate(Instant.now())
        .build();

def transactions = getReserveTransactions(myReserve, filter, 0, 10);
transactions.each { txn ->
    println "Transaction: ${txn.type} - Amount: ${txn.amount} - Date: ${txn.createdAt}";
}
```
 
### 5.4.9. getReserveTransactionsCount

Gets the total number of transactions for a reserve, based on filters.

    Description: Returns the count of transactions associated with a reserve that match the specified filtering criteria.

    Input Parameters:

        reserveId: UUID (the ID of the reserve).

        filter: ReserveTransactionsFilter (criteria for counting transactions).

    Output Parameters:

        Integer: The total number of matching transactions.

Example:
```groovy
def myReserveId = UUID.fromString("some-reserve-uuid");
def filterForCount = ReserveTransactionsFilter.builder()
        .fromDate(Instant.now().minus(90, ChronoUnit.DAYS))
        .build();

def transactionCount = getReserveTransactionsCount(myReserveId, filterForCount);
println "Total transactions in last 90 days: ${transactionCount}";
```
 
### 5.4.10. deleteReserve

Deletes a reserve by its ID.

    Description: Removes a specified reserve. This operation typically requires the reserve to be empty or its funds to be transferred out first, depending on banking system rules.

    Input Parameters:

        reserveId: UUID (the ID of the reserve to delete).

    Output Parameters:

        void: This function does not return a value.

Example:
```groovy
def reserveToDeleteId = UUID.fromString("reserve-to-delete-uuid");
deleteReserve(reserveToDeleteId);
println "Reserve ${reserveToDeleteId} deleted.";
```
 
## 5.5. Utility Functions

These functions provide general utility capabilities within PayScript.

### 5.5.1. callScript

Calls another PayScript script from within the current script.

    Description: Enables modularity and script reuse by allowing one PayScript to invoke another.

    Input Parameters:

        scriptId: String (the ID or name of the script to call).

        params: List<Map<String, Object>>... (a variadic list of maps, where each map represents a set of parameters to be passed to the called script).

    Output Parameters:

        Object: The return type is generic Object to accommodate various possible return values from the called script (e.g., PaymentInfo, LockInfo, Boolean, String, etc.). The consumer of this function must cast the result to the expected type.

Example:
```groovy
// Assume 'processPaymentScript' is another deployed PayScript that returns PaymentInfo
def paymentResult = callScript("processPaymentScript", [payer: 'scan:12345678', payee: 'scan:87654321', amount: 50.00G]);

if (paymentResult instanceof PaymentInfo) {
    def info = (PaymentInfo) paymentResult;
    println "Called script returned Payment ID: ${info.id} with status: ${info.status}";
} else if (paymentResult instanceof LockInfo) {
    def lockInfo = (LockInfo) paymentResult;
    println "Called script returned Lock ID: ${lockInfo.id} with amount: ${lockInfo.amount.amount}";
} else {
    println "Called script returned: ${paymentResult}";
}

// Example with multiple parameter sets if the called script handles multiple triggers/scenarios
// def anotherResult = callScript("anotherScript", [input1: "value1"], [input2: 123]);
```
 
### 5.5.2. logMessage

Logs messages from within the script.

    Description: Provides a mechanism for scripts to output informational, warning, or error messages to the underlying logging system of the PayScript interpreter or Quant Flow platform. These messages are crucial for debugging, monitoring, and auditing script execution.

    Input Parameters:

        message: String (the message string to be logged).

    Output Parameters:

        void: This function does not return a value.

Example:
```groovy
def transactionAmount = new BigDecimal("25.00");
logMessage("Initiating a new transaction for amount: ${transactionAmount}");
```
 
# 6. Security and Compliance

Security and compliance are paramount in financial programming. PayScript is designed with these considerations, relying on the underlying Quant Flow platform and the banking system it integrates with.

## 6.1. Data Handling

PayScript operates within a secure, isolated environment. Sensitive data (e.g., PANs, account numbers) are handled by the core banking system. PayScript scripts primarily interact with these through abstract AccountIdentifier types and built-in functions, reducing the direct exposure of raw sensitive data within the script code itself. All data passed to and from built-in functions is validated by the interpreter and underlying systems.

## 6.2. Transactional Integrity

Financial operations initiated by PayScript (e.g., createPayment, createLock) are designed to be atomic and consistent at the core banking system level. The platform ensures that either the entire operation succeeds or it fails entirely, maintaining data integrity. Idempotency (the ability to execute the same operation multiple times without causing unintended side effects) is a crucial aspect of the underlying financial APIs that PayScript interfaces with.

## 6.3. Auditing Considerations

All actions performed by PayScript scripts, particularly those involving financial transactions or state changes (payments, locks, reserve operations), are subject to auditing and logging by the Quant Flow platform and the integrated core banking systems. This provides a comprehensive audit trail for regulatory compliance and forensic analysis. Developers writing PayScript scripts should ensure that their logic adheres to all applicable regulatory requirements (e.g., GDPR, PSD2, AML, KYC) by utilising the appropriate built-in functions and structuring their financial flows correctly.

# 7. Deployment and Execution Environment

PayScript scripts are not standalone applications but rather components deployed and executed within the Quant Flow ecosystem.

## 7.1. PayScript Interpreter

The PayScript Interpreter is the runtime environment responsible for executing PayScript scripts. It parses the Groovy-based DSL, translates built-in function calls into secure interactions with the core banking system via established APIs, and manages script execution lifecycle.
## 7.2. Quant Flow Sandbox

For development, testing, and experimentation, PayScript scripts can be run on the Quant Flow Sandbox. This environment provides a safe and isolated space to write, debug, and test PayScript logic without affecting live banking systems. It includes a PayScript Interpreter configured to simulate core banking interactions.

## 7.3. Quant Flow Product Integration

For production deployment and execution, banks integrate with the Quant Flow Product. This product provides a robust, scalable, and secure PayScript Interpreter service. When a bank purchases the Quant Flow Product, they gain access to this service, which runs deployed PayScript scripts and orchestrates the necessary actions, such as making payments, managing locks, or updating reserves, by communicating with the bank's own core banking infrastructure.

# 8. Examples

## 8.1. Basic Payment Transfer

This example demonstrates a fundamental payment transfer using PayScript, triggered on demand and taking account and amount details as parameters.

 
// Define the trigger for this script, indicating it runs when explicitly invoked
trigger = "on_demand";

// Declare input parameters that will be provided when this script is deployed as an Automation.
// The syntax ${paramName:type} specifies a parameter name and its expected AccountIdentifierType.
def payer  = ${payer:scan};  // Expected format: sortCode:accountNumber or a full AccountIdentifier object
def payee = ${payee:scan};   // Expected format: sortCode:accountNumber or a full AccountIdentifier object
def amount = ${amount:decimal}; // Expected format: a decimal number, e.g., 100.50

// Construct the AccountInfo objects for the payer and payee.
// We assume an AccountInfo builder is available which can take an AccountIdentifier directly.
def payerAccountInfo = AccountInfo.builder()
        .type(AccountIdentifierType.SCAN)
        .identifier(payer) // The 'payer' parameter (String or Map) will be parsed into the appropriate AccountIdentifier structure
        .build();

def payeeAccountInfo = AccountInfo.builder()
        .type(AccountIdentifierType.SCAN)
        .identifier(payee)
        .build();

// Construct the AmountInfo object for the payment.
// We assume the currency is GBP for this example.
def paymentAmountInfo = AmountInfo.builder()
        .currency(CurrencyEnum.GBP) // Fixed currency for this example; could also be a parameter
        .amount(amount)
        .build();

// Construct the PaymentInfo object, which encapsulates all details of the payment.
def payment = PaymentInfo.builder()
        .payer(payerAccountInfo)
        .payee(payeeAccountInfo)
        .amountInfo(paymentAmountInfo)
        .purpose("Automated transfer via PayScript") // A clear purpose for audit trails
        .paymentReference("PS-AUTO-TRANSFER-" + UUID.randomUUID().toString()) // A unique reference for idempotency/tracking
        .build();

// Call the built-in 'createPayment' function to execute the transaction.
// This function interacts with the core banking system.
def newPaymentId = createPayment(payment);

// Optionally, you might retrieve the status of the created payment for logging or further logic.
// However, direct logging/output mechanisms from within the script might be limited and
// often rely on the surrounding Quant Flow platform to capture results or exceptions.
// In a real scenario, you might return this ID or a PaymentInfo object for the platform to process.
// def finalPaymentInfo = getPaymentInfo(newPaymentId);
// println "Payment ${newPaymentId} status: ${finalPaymentInfo.status}";

 
# 9. Conformance

A PayScript implementation conforms to this specification if it:

    Correctly parses and executes all valid PayScript syntax, which is a superset of Groovy syntax.

    Accurately implements the behaviour and return values of all built-in functions as defined in Section 5.

    Properly handles PayScript-specific data types and their interactions.

    Adheres to the described error handling and execution model.

Any deviations from this specification must be clearly documented by the implementer.
# 10. References

    Apache Groovy Documentation

    Open Banking Standard (e.g., UK Open Banking)

    ISO 20022 Financial Services Messaging

    [Quant Flow Product Documentation (Internal)](Placeholder for internal Quant Flow documentation)