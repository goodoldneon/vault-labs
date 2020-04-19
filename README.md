# Vault Workshop

Labs for learning the basics of HashiCorp vault.

## Info Before the Labs

- Vault is a tool for securely accessing secrets.
  - Secrets can be:
    - Arbitrary key/value
    - Database credentials
    - API keys
    - Credentials for service-oriented architecture communication
    - Certificates
- Vault's key features:
  - Arbitrary kev/value storage
    - Filesystem-like paths.
  - Dynamic secrets
    - Generate secrets on-demand.
    - MySQL, AWS, SSH, Active Directory, and many more.
  - Revocation
    - Revoke secrets at any time.
    - Can revoke trees of secrets.
      - Example: revoke all tokens created by a compromised user.
  - Leasing and renewal
    - All secrets in Vault expire after a configurable time period.
  - Data encryption
    - Encrypt and decrypt data without storing it.
    - For example, store encrypted data in MySQL without having to design your own encryption methods.
    - Key rotation doesn't affect app and DB layers.

## Labs

1. [Secrets CRUD Operations](labs/secrets-crud)
2. [Authentication and Policies](labs/auth-and-policies)
3. [Dynamic Secrets](labs/dynamic-creds)

## Other Features

- Audit devices
  - A detailed log of all requests and response to Vault.
  - Because every operation with Vault is an API request/response, the audit log contains every authenticated interaction with Vault, including errors.
