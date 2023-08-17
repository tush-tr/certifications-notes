- [Data States](#data-states)
- [Encryption](#encryption)
  - [Symmetric Key Encryption](#symmetric-key-encryption)
  - [Asymmetric Key Encryption](#asymmetric-key-encryption)
- [Cloud KMS(Key management service)](#cloud-kmskey-management-service)
- [KMS in Console](#kms-in-console)
  - [How to use a created key](#how-to-use-a-created-key)
# Data States
- Data at rest:
  - Stored on a device or a backup
    - data on a hard disk, in a database, backups, archives
- Data in motion:
  - Being transferred across a network
    - also called data in transit
  - Examples:
    - Data copied from on-premise to cloud storage
    - An application talking to database.
  - Two types:
    - In and out of the cloud (from internet)
    - Within Cloud
- Data in use:
  - Active data processed in a non-persistent state
    - Example: Data in your RAM
# Encryption
- If we store data as is, what would happen if an unauthorized entity gets access to it?
  - Imagine losing an unencrypted hard disk
- First law of security: Defence in Depth
- Typically, enterprises encrypt all data
  - Data on your hard disks
  - Data in your databases
  - Data on your file servers
- Is it sufficient if you encrypt data at rest?
  - No. Encrypt data in transit - between application to database as well.

## Symmetric Key Encryption
- Use the same key for encryption and decryption.
- Key factor 1: Choose the right encryption algorithm
- Key Factor 2: How do we secure the encryption key?
- Key Factor 3: How do we share the encryption key?

## Asymmetric Key Encryption
- Different key for encryption and different key for decryption
- Two keys: Public key and private key
- Also called public key cryptography
- Encrypt data with public key and decrypt with private key
- Share public key with every body and keep the private key with you.


# Cloud KMS(Key management service)
- Create and manage cryptographic keys (symmetric and asymmetric)
- Control their use in your application and GCP Services.
- Provides an API to encrypt, decrypt, or sign data.
- Use existing cryptographic keys created on premises.
- Integrates with almost all GCP services that need data encryption:
  - Google-Managed key: No configuration required
  - Customer-Managed Key: Use key from KMS
  - Customer-Supplied key: Provide your own key

# KMS in Console
- Enable Cloud Key Management Service(KMS) API
- Security>Cryptographic keys
- Create a key ring(attach multiple keys in a key ring)
- Global or regional
- Key type:
  - Generated key: KMS will create a key
  - Imported Key: import key
  - External Key manager: Not available for global only for regional
- Protection level: 
  - HSM: Hardware 
  - Software level
- Purpose
  - Symmetric encrypt/decrypt
  - Asymmetric sign
  - Asymmetric decrypt
## How to use a created key
- In VM instance creation console
- Security: Encryption(Google-Manged-Key)
- Select: Customer managed key
  - Add key path from KMS
  - Service account should have access to KMS

