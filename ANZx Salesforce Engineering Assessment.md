# ACME Bank

_Scenario:_ ACME Bank plans to build a light-weight Salesforce application for our branch staff to use. Salesforce has been selected as the platform for branch staff and you have been hired to build out the system.

_Format:_ Salesforce DX Project

_Time:_ Should take no longer than 3-4 hours

_Submission format:_ Please push into a public git repo (GitHub, GitLab etc) and share the link with the hiring manager

_Version:_ 0.6

It is important to note that the expectation is the implementation should be of a _production-ready_ standard. The outcome should be a finished Salesforce Org that a branch staff member can open and use to manage their customers, view their account information and open new accounts. We should be able to build a scratch org from a definition file and push the metadata to test the functional output of this assessment (any additional steps should be recorded in the project README).

Please **DO NOT** use financial services cloud or any managed packages. This assessment is intended to check your configuration/customisation choices, and we are aware that this use case lends itself to FSC but please configure this from scratch.

## Part 1: Modelling
### Configuration

Firstly we want to model 3 parts of the customer model:

1. The customers themselves, which are _only_ be individuals
2. All Customers can have Financial Accounts, but only of types Spending, Savings, Credit or Loan
3. All Financial Accounts can have a transaction history and a calculated balance (balance at account creation is zero dollars, and each transaction record modifies this balance).

## Part 2: Provisioning
### Platform Automation

When a customer account is created, we need to give them an Acme Bank Unique ID (ABID). The unique identifier must meet the following requirements:

1. The ID is unique for all customers created in the system
2. The ID must be 12 characters in length, with the first 3 being alpha and the last 9 being numeric e.g XXX 000 000 000
3. In the event of data loss and restoration, the record must be restorable with the same ID
4. The numbers **must not** be deterministic (ie I should not be able to guess a customer's registration number)

The requirements for the overall capability:

1. When a customer record is created, the ABID should be auto generated. This number is not editable by end users
2. The customer data should then be published on a feed that can be consumed by external services. However the only fields that should be published are:
    1. Name
    2. Address
    3. Email and Phone
    4. ABID

## Part 3: Receiving Transactions
### Integration

The transactions related to these financial accounts will live within Salesforce so that they are available to internal users. Transactions are used to calculate the balance, and can either be a credit (money added to the balance) or debit (money subtracted from the balance). The following is an example of a single transaction record:

```json
{
    "abid": "ACM 123 456 789",
    "accountNumber": 18171615,
    "amount": 125.68,
    "currency": "AUD",
    "date": "2020/01/01",
    "merchantABN": 123456789,
    "merchantBSB": 123456,
    "merchantName": "Beau Flowers",
    "time": "17:32:25",
    "type": "debit"
}
```

> **NOTE** `accountNumber` is the number of the financial account, and `type` will either have a value of `credit` or `debit`

1. Please create a service that can accept transactions being passed to Salesforce
2. The transactions should be stored on platform
3. The service should be idempotent
4. The financial account balances should be calculated by the transactions being fed into the system
5. Consider the sheer volume of data as well as the high volume of transactions in your design