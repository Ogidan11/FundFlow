# FundFlow- Decentralized Beneficiary Management & Donation Tracking Contract (Clarity)

This Clarity smart contract provides a secure and decentralized platform for managing beneficiaries, tracking donations, handling fund utilization, and controlling roles. It ensures transparency and governance over charitable operations through defined roles like Admin, Moderator, and Beneficiary.

---

## 📜 Table of Contents

- [Overview](#overview)
- [Roles & Permissions](#roles--permissions)
- [Core Data Structures](#core-data-structures)
- [Key Functions](#key-functions)
  - [Role Management](#role-management)
  - [Beneficiary Management](#beneficiary-management)
  - [Donation System](#donation-system)
  - [Utilization Tracking](#utilization-tracking)
- [Read-Only Functions](#read-only-functions)
- [Error Codes](#error-codes)
- [Contract Initialization](#contract-initialization)
- [Security Checks](#security-checks)
- [Development Notes](#development-notes)

---

## 📌 Overview

This smart contract allows:

- Admins to manage roles and approve fund utilizations.
- Moderators to register new beneficiaries.
- Donors to donate to registered beneficiaries.
- Transparent tracking of donations and fund utilizations.

Built using the [Clarity language](https://docs.stacks.co/write-smart-contracts/clarity-language), the contract enforces permission checks and error handling to ensure reliable and secure interactions.

---

## 🛡️ Roles & Permissions

The system is role-based, with increasing levels of authority:

| Role         | Value | Permissions                                                                 |
|--------------|-------|------------------------------------------------------------------------------|
| `ROLE-ADMIN` | `u1`  | Can set/remove roles, approve utilizations, and perform all moderator actions |
| `ROLE-MODERATOR` | `u2` | Can register beneficiaries                                                   |
| `ROLE-BENEFICIARY` | `u3` | Can be assigned as beneficiary only (no specific functions yet)            |

---

## 🧱 Core Data Structures

### `roles`
Maps a user to a role:
```clarity
{ user: principal } => { role: uint }
```

### `beneficiaries`
Stores registered beneficiary details:
```clarity
{
  id: uint,
  name: string,
  description: string,
  target-amount: uint,
  received-amount: uint,
  status: string
}
```

### `donations`
Stores donation records:
```clarity
{
  id: uint,
  donor: principal,
  beneficiary-id: uint,
  amount: uint,
  timestamp: uint
}
```

### `utilization`
Stores fund utilization entries:
```clarity
{
  id: uint,
  beneficiary-id: uint,
  milestone: uint,
  description: string,
  amount: uint,
  status: string
}
```

---

## 🧩 Key Functions

### 🔐 Role Management

#### `set-role (user principal) (new-role uint)`
Sets a role for a user. Only callable by contract owner.

#### `remove-role (user principal)`
Removes a user’s role. Only callable by contract owner.

---

### 👥 Beneficiary Management

#### `register-beneficiary (name string) (description string) (target-amount uint)`
Registers a new beneficiary. Only moderators or higher can call this.

#### `get-beneficiary (id uint)`
Returns beneficiary details.

---

### 💰 Donation System

#### `donate (beneficiary-id uint) (amount uint)`
Allows any user to donate to a registered beneficiary. It updates both donation and beneficiary records.

#### `get-donation-by-id (donation-id uint)`
Returns a specific donation record.

#### `get-donation-count`
Returns the total number of donations.

---

### 📊 Utilization Tracking

#### `add-utilization (beneficiary-id uint) (description string) (amount uint)`
Admins can record how funds will be used.

#### `approve-utilization (beneficiary-id uint) (milestone uint)`
Admins approve utilization milestones after verifying available funds.

#### `get-utilization-by-id (utilization-id uint)`
Returns a utilization record.

#### `get-utilization-count`
Returns the total number of utilization entries.

---

## 📟 Read-Only Functions

- `get-beneficiary`
- `get-donation-by-id`
- `get-utilization-by-id`
- `get-donation-count`
- `get-utilization-count`

---

## ❗ Error Codes

| Code       | Description                |
|------------|----------------------------|
| `u100`     | Not authorized              |
| `u101`     | Already registered          |
| `u102`     | Not found                   |
| `u103`     | Insufficient funds          |
| `u104`     | Beneficiary not found       |
| `u105`     | Utilization not found       |
| `u106`     | Invalid input               |

---

## 🏁 Contract Initialization

The contract uses `initialize-contract` to:

- Assign `ROLE-ADMIN` to the contract deployer.
- Set `contract-owner` to the deployer (`tx-sender`).

This function is called once during contract deployment.

---

## 🔒 Security Checks

- Users can’t change/remove their own roles.
- Only admins can assign roles.
- Validations on all inputs and IDs.
- Donations and utilizations validated against available data and balances.
- Milestone approvals require sufficient funds.

---
