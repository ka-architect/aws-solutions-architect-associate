# Encryption

## Introduction
- In-flight Encyrption: TLS/SSL, encrypting data when being sent
- Encryption at Rest: server side encryption, ensures data stored is encrypted, handled via encryption keys
- Client Side Encryption: data is encrypted and decrypted by the client, envelope encryption

## AWS Key Management Service (KMS)
- Managed key encryption service, which controls data access
- KMS is available via API calls, to encrypt and decrypt progmatically, no direct access to keys
- Key Types: Symmetric and Asymetric
- **Symmetric Keys:** single key to encrypt and decrypt (AES-256)
- **Asymmetric Keys:** public and private key pair, public key used to encrypt, private key used to decrypt
    - public key is downloadable, no access to private key unencrypted
    - encrypt at client with public key, send public key to server to decrypt
- Types of KMS Keys: AWS Owned, AWS Managed, Customer Managed (custom keys created in KMS or imported)
- Key rotations: yearly and automatic for AWS keys only, rotation must be enabled for customer keys
- **Cross-Region Keys:** KMS keys are regional, but can be enabled in another region
    - Take snapshot of encrypted data in region A with Key A
    - Use KMS rencryption API to decrypt with Key A and encrypt with Key B
    - Restore snaphot from region A to region B with key B
- **KMS Key Policies:** control access to keys, but cannot control access without them
    - **Default KMS Key Policy:** created if no key policy defined, if root has complete access then account has complete access
    - **Custom KMS Key Policy:** define user/role access to key, enable cross-account
- **Multi-Region KMS Key:** allow same key to be used cross-region
    - Primary key id is replicated across regions
    - Managed only within primary region
    - Update key policy

## Resource Encryption Examples
- Copy Snapshot Across Accounts
    - Take encrypted snapshot
    - Attach KMS Key policy to allow access to cross-account access and decrypt
    - Share snapshot to target account
    - In target, create copy of snapshot, decrypt with old key, encrypt with new key
    - Create volume in target from snapshot
- Aurora encryption at rest is done with encryption SDK
- S3 replication creates heavy load on KMS API
    - Unencrypted and SSE-S3 objects are replicated by default, enable replication for SSE-KMS objects
- AMI can also be encrypted, to share AMI:
    - Encrypted AMI needs to be modified to add launch permission for target account
    - Share KMS keys from source to target account, modify IAM role and key policy to allow KMS API calls
    - Target account can launch EC2 with AMI with same key, and ability to encrypt with new key