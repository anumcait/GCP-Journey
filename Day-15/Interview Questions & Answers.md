## Q1. Why should secrets not be hardcoded?
➡️ Hardcoding exposes them to leaks, makes rotation difficult, and violates security standards.

## Q2. How does GCP Secret Manager secure secrets?
➡️ Secrets are encrypted, access-controlled via IAM, and auditable with Cloud Audit Logs.

## Q3. How do you rotate secrets automatically?
➡️ Configure rotation period and next rotation time in GCP.

## Q4. What’s the difference between a secret and a secret version?
➡️ The secret is the container; each version stores a new value (like revisions).

## Q5. How can you integrate Secret Manager into applications?
➡️ Using SDKs, REST API, or CLI commands, authenticated via service accounts.
