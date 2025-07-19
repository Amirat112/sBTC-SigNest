# sBTC-SigNest

A secure multi-signature wallet contract written in Clarity for the Stacks blockchain. This smart contract enables M-of-N authorization for executing STX transfers, managing owners, setting approval thresholds, and batch processing transactions.

---

## 📋 Features

* ✅ Multi-signature transaction execution (M-of-N)
* ✅ Submit transactions with optional memo and custom expiration
* ✅ Cancel pending transactions
* ✅ Owner management (add/remove)
* ✅ Adjustable signature threshold
* ✅ Batch transaction creation
* ✅ Transparent on-chain transaction tracking
* ✅ Event logging for key actions

---

## 🔐 Contract Initialization

The contract must be initialized by calling `initialize`, providing:

* A list of initial owners (max 20)
* A threshold (M) of required signatures to execute transactions

```clojure
(initialize (list 'owner1 'owner2 'owner3) u2)
```

---

## 📦 Transaction Lifecycle

### 1. Submit a Transaction

```clojure
(submit-transaction 'recipient-principal u10000)
```

Or with memo and custom expiration:

```clojure
(submit-transaction-with-memo 'recipient-principal u10000 "Payment for work" u120)
```

### 2. Sign the Transaction

```clojure
(sign-transaction u0) ;; `u0` is the transaction ID
```

Only owners can sign, and no double signing is allowed.

### 3. Execute the Transaction

```clojure
(execute-transaction u0)
```

Once the threshold number of signatures is reached and the transaction has not expired.

---

## 📦 Batch Transactions

You can create multiple pending transactions in a single call:

```clojure
(batch-create-transactions 
  (list 'rec1 'rec2)
  (list u1000 u2000)
  (some (list "Memo1" "Memo2")))
```

If `memos` is `none`, default "Transfer" memos will be applied.

---

## ⚙️ Owner Management

### Add a New Owner

```clojure
(add-owner 'new-owner)
```

### Remove an Owner

```clojure
(remove-owner 'owner-to-remove)
```

You must not reduce owners below the required signature threshold.

---

## 🔄 Signature Threshold

### Update Signature Threshold

```clojure
(update-required-signatures u3)
```

Only possible if there are no active (pending) transactions.

---

## ⏱ Expiration Handling

Transactions have a default expiration of 144 blocks (\~24 hours). This can be overridden using `submit-transaction-with-memo`.

If a transaction expires, it cannot be executed or signed further.

---

## 📖 Read-Only Functions

```clojure
(get-transaction u0)
(get-required-signatures)
(get-total-owners)
(is-valid-owner 'principal)
```

---

## ❌ Error Codes

| Code | Error                     |
| ---- | ------------------------- |
| u1   | Not authorized            |
| u2   | Invalid signature         |
| u3   | Already signed            |
| u4   | Insufficient signatures   |
| u5   | Invalid threshold         |
| u6   | Transaction not found     |
| u7   | Max signers exceeded      |
| u8   | List overflow             |
| u9   | Transaction not pending   |
| u10  | Already an owner          |
| u11  | Not an owner              |
| u12  | Invalid owner threshold   |
| u13  | Transaction expired       |
| u14  | Invalid signature count   |
| u15  | Active transaction exists |
| u16  | Insufficient funds        |
| u17  | Invalid expiration        |

---

## 🧪 Example Workflow

```clojure
;; Initialize with 3 owners, 2 required signatures
(initialize (list 'wallet.alice 'wallet.bob 'wallet.charlie) u2)

;; Submit a transaction
(submit-transaction 'wallet.dave u50000)

;; Sign the transaction
(sign-transaction u0) ;; alice
(sign-transaction u0) ;; bob

;; Execute the transaction
(execute-transaction u0)
```

---

## 🧠 Security Notes

* Only owners can sign, submit, execute, or manage ownership.
* Transactions expire to prevent stale approvals.
* Memos and expiration are optional but provide audit transparency.
* Prevents double signing and maxes out signer list to 20.

---
