# 🧠 Day 15: Secrets Manager – Storing API Keys Safely in GCP

Google Cloud **Secret Manager** allows you to store, manage, and access sensitive information — such as API keys, passwords, or certificates — securely.  
Here, we’ll learn **why**, **how**, and **what** to do when working with secrets in GCP.

---

## 📍 Why Not to Hardcode Passwords or Tokens

Hardcoding secrets (like API keys or credentials) directly into source code is a **major security risk**.

```diff
- api_key = "12345-SECRET-KEY" -- This api key is hardcoded
```

> “Hardcoding secrets is like writing your ATM PIN on your debit card 💳.  
If your code is leaked, your keys go with it.  
Instead, store them securely in Secret Manager — where they’re encrypted, access-controlled, and logged.”





