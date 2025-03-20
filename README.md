# CN DID Method Specification

Version 1.0.0

## 1. Overview

The `did:cn` method is a decentralized identifier method designed for China's industrial internet identity system. It is based on the industrial internet identification system, integrating traditional industrial identifiers (such as "88.100.1") into the W3C DID architecture, enabling verifiable and self-managed digital identities.

### 1.1 Objectives

- Provide verifiable digital identities for China's industrial internet entities
- Support seamless integration between industrial internet identifiers and the DID ecosystem
- Ensure data sovereignty and privacy protection
- Establish trusted industrial internet identity infrastructure

## 2. Namespace Specific Identifier (NSI)

### 2.1 Format Specification

The `did:cn` method identifiers adhere to the following ABNF syntax:

```
cn-did = "did:cn:" cn-specific-idstring
cn-specific-idstring = issuer "." industry "." enterprise 
issuer = 2DIGIT                     ; Issuing authority, fixed 2 digits
industry = 3*5DIGIT                 ; Industry code, 3-5 digits
enterprise = 1*128DIGIT             ; Enterprise code, 1-128 digits
```

### 2.2 Example

`did:cn:88.100.1` 

## 3. DID Operations

### 3.1 Create

Enterprises or organizations create DIDs through the following steps:

1. Apply for an industrial internet identifier from an authorized issuing authority
2. Generate a key pair as the DID control credential
3. Construct a DID document in accordance with the specification
4. Submit the DID document to the national blockchain registry
5. The DID document is permanently recorded on the blockchain and assigned a unique transaction hash
6. The system returns creation confirmation and transaction proof

Request example:

```
POST /did/register
Content-Type: application/json

{
  "did": "did:cn:88.100.12345",
  "didDocument": {
    // DID document content
  },
  "signature": {
    "type": "Ed25519Signature2020",
    "created": "2025-03-20T14:30:00Z",
    "verificationMethod": "did:cn:88.100.12345#key-1",
    "signatureValue": "zQcJe..."
  }
}
```

### 3.2 Read

The DID is resolved through the following steps:

1. Client sends a DID resolution request
2. Resolver identifies the "did:cn" prefix
3. Queries the national blockchain registry
4. The blockchain network returns the stored DID document
5. Verifies the blockchain proof and digital signature of the DID document
6. Returns the verified DID document

Request example:

```
GET /did/resolve/did:cn:88.100.12345
Accept: application/did+json
```

### 3.3 Update

DID controllers can update the DID document through these steps:

1. Prepare the update content
2. Sign the update request using the control key
3. Submit the update request to the national blockchain registry
4. The blockchain network verifies the signature and update permissions
5. Creates a new blockchain transaction recording the update operation
6. Records the linkage between the original DID document and the updated version
7. The blockchain network reaches consensus, permanently recording the update history

Request example:

```
PUT /did/update
Content-Type: application/json

{
  "did": "did:cn:88.100.12345",
  "operation": "update",
  "previousTransaction": "0x1a2b3c...", // Hash reference to the previous transaction
  "updateData": {
    "addServices": [
      {
        "id": "did:cn:88.100.12345#api",
        "type": "api",
        "serviceEndpoint": "https://api.example.com"
      }
    ]
  },
  "signature": {
    "type": "Ed25519Signature2020",
    "created": "2025-03-20T16:30:00Z",
    "verificationMethod": "did:cn:88.100.12345#key-1",
    "signatureValue": "zQcJe..."
  }
}
```

### 3.4 Deactivate

DID controllers can deactivate the DID through the following steps:

1. Prepare the deactivation request
2. Sign the deactivation request using the control key
3. Submit the deactivation request to the national blockchain registry
4. The blockchain network verifies the signature and deactivation permissions
5. Creates a blockchain transaction recording the deactivation operation
6. Records the DID status change to "deactivated"
7. The deactivation operation is permanently recorded on the blockchain, ensuring immutability

Request example:

```
PUT /did/deactivate
Content-Type: application/json

{
  "did": "did:cn:88.100.12345",
  "operation": "deactivate",
  "previousTransaction": "0x4d5e6f...", // Hash reference to the previous transaction
  "reason": "Business termination",
  "signature": {
    "type": "Ed25519Signature2020",
    "created": "2025-03-20T17:30:00Z",
    "verificationMethod": "did:cn:88.100.12345#key-1",
    "signatureValue": "zQcJe..."
  }
}
```

## 4. DID Document Example

```
{
  "@context": [
    "https://www.w3.org/ns/did/v1",
    "https://w3id.org/security/suites/ed25519-2020/v1",
  ],
  "id": "did:cn:88.100.1",
  "controller": "did:cn:88.100.1",
  "verificationMethod": [
    {
      "id": "did:cn:88.100.1#key-1",
      "type": "Ed25519VerificationKey2020",
      "controller": "did:cn:88.100.1",
      "publicKeyMultibase": "z6MkhaXgBZDvotDkL5257faiztiGiC2QtKLGpbnnEGta2doK"
    },
  ],
  "authentication": [
    "did:cn:88.100.1#key-1"
  ],
  "assertionMethod": [
    "did:cn:88.100.1#key-1"
  ],
  "service": [
    {
      "id": "did:cn:88.100.1#web",
      "type": "LinkedDomains",
      "serviceEndpoint": "https://example.com/"
    }
  ]
}
```

## 5. Security Considerations

- **Public Key Integrity**: By storing the public keys associated with the DID on the blockchain, their integrity is ensured, making them resistant to tampering.
- **Private Key Protection**: Private keys used to generate the DID document must be securely stored and not exposed to unauthorized parties.

## 6. Privacy Considerations

- **Data Minimization**: The DID method should aim to store only necessary information on the blockchain, such as public keys and related service endpoints. Sensitive information should be avoided in the DID document itself.
- **Blockchain Transparency**: While blockchains provide immutability and security, they are also public and transparent. This could potentially expose information about DID creation and usage. To mitigate this, off-chain storage can be used to store sensitive information and link DID documents to off-chain data in a privacy-preserving manner.
- **Access Control**: Access to the DID and its associated services should be controlled through cryptographic methods, ensuring that only authorized parties can modify or read sensitive data associated with the DID.