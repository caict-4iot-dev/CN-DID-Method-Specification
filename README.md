# CN DID Method Specification

Version 1.0.0

## 1. Overview

The `did:cn` method defines a DID method for `.cn` domain names, which associates a second-level domain with a SHA-256-based hash to form a decentralized identifier (DID). The hash represents a transaction or public key associated with the domain, ensuring that each DID is unique and verifiable.

- **Prefix**: `did:cn`
- **Purpose**: To create decentralized identifiers for `.cn` domain names, linking them with public keys stored on the blockchain.

------

## 2. DID Method Name

The method name is `did:cn`

This prefix is fixed and identifies the DID method used for this namespace.

------

## 3. Namespace Specific Identifier (NSI)

The **Namespace Specific Identifier (NSI)** for the `did:cn` method consists of two main components:

1. **Second-level domain**: The second-level domain extracted from the `.cn` domain (e.g., `baidu` from `baidu.cn`).
2. **Hash**: A SHA-256 hash generated from the domain's public key or blockchain transaction. This ensures that the DID is unique and verifiable.

### 3.1 ABNF Representation

The structure of a `did:cn` identifier follows this format:

```
did-cn = "did:cn:" second-level-domain ":" hash

second-level-domain = 1*63(ALPHA / DIGIT / "-") #  A valid second-level `.cn` domain name, consisting of 1 to 63 characters, which can include letters, digits, and hyphens (`-`).

hash = 64(HEXCHAR) # A SHA-256 hash represented as a 64-character hexadecimal string (256 bits).

HEXCHAR = DIGIT / "a" / "b" / "c" / "d" / "e" / "f" / "A" / "B" / "C" / "D" / "E" / "F"

ALPHA = %x41-5A / %x61-7A    ; A-Z / a-z

DIGIT = %x30-39            ; 0-9
```

### 3.2 Explanation of Components

- **`did-cn`**: This is the fixed prefix that identifies the method (`did:cn`).
- **`second-level-domain`**: This part is the second-level `.cn` domain, such as `baidu` in `baidu.cn`. It can contain alphanumeric characters and hyphens, and its length must be between 1 and 63 characters.
- **`hash`**: This is a 64-character SHA-256 hexadecimal hash, which uniquely identifies the DID. It is derived from the public key or blockchain transaction related to the second-level domain.

------

## 4. DID Document

### 4.1 Structure of DID Document

The DID document associated with a `did:cn` identifier contains essential information for verifying the identity, including public keys in **JWK** (JSON Web Key) format and other metadata. The document is stored on the blockchain to ensure immutability.

#### Example DID Document with Ed25519 and JWK Format

```
json复制代码{
  "id": "did:cn:baidu:3f8a1d4d3b5b9f45eaf658b97c0183f23c9fa5a2be1b426d77b7f4e4f3e75a04",
  "publicKey": [
    {
      "id": "did:cn:baidu:3f8a1d4d3b5b9f45eaf658b97c0183f23c9fa5a2be1b426d77b7f4e4f3e75a04#keys-1",
      "type": "Ed25519VerificationKey2018",
      "controller": "did:cn:baidu:3f8a1d4d3b5b9f45eaf658b97c0183f23c9fa5a2be1b426d77b7f4e4f3e75a04",
      "publicKeyJwk": {
        "kty": "OKP",
        "crv": "Ed25519",
        "x": "bZq5n9sBe15AacV9Jh39vDtXWjVZzq4AtS7FqEDm8DE"
      }
    }
  ],
  "authentication": [
    {
      "type": "Ed25519SignatureAuthentication2018",
      "publicKey": "did:cn:baidu:3f8a1d4d3b5b9f45eaf658b97c0183f23c9fa5a2be1b426d77b7f4e4f3e75a04#keys-1"
    }
  ],
  "service": [
    {
      "type": "LinkedDomains",
      "serviceEndpoint": "https://baidu.cn/service"
    }
  ]
}
```

### 4.2 Explanation of the DID Document Structure

- **`id`**: The DID identifier itself (e.g., `did:cn:baidu:3f8a1d4d3b5b9f45eaf658b97c0183f23c9fa5a2be1b426d77b7f4e4f3e75a04`).
- **`publicKey`**: A list of public keys, represented in **JWK** format (e.g., Ed25519 public key).
- **`authentication`**: The list of authentication methods, specifying the key used for identity verification.
- **`service`**: A list of service endpoints that can be used to interact with the DID.

In the above example, the **publicKey** section uses the **Ed25519** algorithm in **JWK** format:

- **`kty`**: The key type (`OKP` for elliptic curve keys).
- **`crv`**: The curve used, which is `Ed25519` for this key.
- **`x`**: The public key value in base64url encoding.

------

## 5. DID Operations

### 5.1 Create

#### 5.1.1  Domain-to-DID Conversion

To create a `did:cn` DID:

1. Extract the second-level domain from the `.cn` domain (e.g., `baidu` from `baidu.cn`).
2. Generate a public-private key pair using **Ed25519** for the domain.
3. Generate a SHA-256 hash of the public key or related transaction (e.g., `3f8a1d4d3b5b9f45eaf658b97c0183f23c9fa5a2be1b426d77b7f4e4f3e75a04`).
4. Store the DID document (including the public key in JWK format) on the blockchain.
5. Form the DID by combining the second-level domain and the hash (e.g., `did:cn:baidu:3f8a1d4d3b5b9f45eaf658b97c0183f23c9fa5a2be1b426d77b7f4e4f3e75a04`).

#### 5.1.2 Blockchain Storage

The DID document is stored on the blockchain to ensure immutability and verifiability. The blockchain serves as a decentralized ledger, providing a trusted source of truth for the DID and associated metadata.

### 5.2 Read

#### 5.2.1 Resolving a DID

To resolve a `did:cn` identifier:

1. Extract the second-level domain (e.g., `baidu`) and the hash (e.g., `3f8a1d4d3b5b9f45eaf658b97c0183f23c9fa5a2be1b426d77b7f4e4f3e75a04`).
2. Query the blockchain to retrieve the DID document associated with the hash.
3. Verify the authenticity of the DID document by checking the public key and its associated cryptographic signatures.

#### 5.2.2 Verification Process

To verify a `did:cn` DID:

1. Ensure that the public key listed in the DID document matches the one associated with the DID.
2. Use the public key for authentication purposes (e.g., signing, verifying, or encrypting messages).
3. Confirm that the DID document has not been tampered with by cross-referencing it with the blockchain.

### 5.3 Update

The **Update** operation allows updating the DID document by submitting a new transaction to the blockchain. This typically involves updating the public key or service endpoints. To perform the update:

1. Generate a new public-private key pair.
2. Submit the new public key or document update to the blockchain.
3. The updated DID document will be linked to the new transaction hash.

### 5.4 Deactivate

The **Deactivate** operation is used to invalidate a DID. This can be achieved by marking the DID document as inactive on the blockchain, ensuring that the DID and associated keys are no longer usable for authentication or service access.

------

## 6. Use Cases

### 6.1 Domain Name Conversion

This method allows for converting `.cn` domain names into DIDs that can be associated with cryptographic identities. For example:

- `did:cn:baidu:3f8a1d4d3b5b9f45eaf658b97c0183f23c9fa5a2be1b426d77b7f4e4f3e75a04` could represent the identity of the domain `baidu.cn`, with the public key and other metadata securely stored on the blockchain.

### 6.2 Blockchain-based Identity Verification

The `did:cn` method is useful for verifying the identity of organizations or entities represented by `.cn` domain names. It ensures that the DID is tamper-proof and verifiable on the blockchain.

### 6.3 Service Access Control

By associating public keys with service endpoints in the DID document, access to various services can be authenticated and controlled using blockchain-backed DIDs.

------

## 7. Security Considerations

- **Public Key Integrity**: The integrity of the public key associated with the DID is ensured by storing it on the blockchain, making it resistant to tampering.
- **Private Key Protection**: The private key used to generate the DID document must be securely stored and never exposed to unauthorized parties.

------

## 8. Privacy Considerations

- **Data Minimization**: The DID method should aim to store only the necessary information on the blockchain, such as the public key and the associated service endpoints. Sensitive information should be avoided in the DID document itself.
- **Blockchain Transparency**: While the blockchain offers immutability and security, it is also public and transparent. This could potentially expose information about DID creation and usage. To mitigate this, off-chain storage can be used for sensitive information, with the DID document linking to the off-chain data in a privacy-preserving way.
- **Access Control**: Access to the DID and its associated services should be controlled via cryptographic methods, ensuring that only authorized parties can modify or read sensitive data associated with the DID.

## 9. References

1. W3C DID Specification: https://www.w3.org/TR/did-core/
