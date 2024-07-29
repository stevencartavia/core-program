# Week 1 - Cryptographic Basics

## Practical

### Write a circuit to prove multiplication

From there, let's try to write a circuit to prove the multiplication of two numbers!

Once that works, try to make a circuit to prove the multiplication of three numbers.

````circom 
pragma circom 2.1.6;

template Multiply() {
    // declaration of signals
    signal input a;
    signal input b;
    signal output mult;

    // constraint
    mult <== a * b;
}

component main = Multiply();

/* INPUT = {
    "a": 5,
    "b": 5
} */
````

````circom
pragma circom 2.1.6;

template Multiply2() {
    // declaration of signals
    signal input a;
    signal input b;
    signal input c;
    signal intermediate;
    signal output product;

    // constraint
    intermediate <== a * b;
    product <== intermediate * c;
}

component main = Multiply2();

/* INPUT = {
    "a": 5,
    "b": 5,
    "c": 2
} */ 
````

### Write a circuit to prove input to hash

````circom
pragma circom 2.1.6;

include "circomlib/poseidon.circom";

template hashProof () {
    signal input preimage;
    signal input hash;

    component hasher = Poseidon(1);
    hasher.inputs[0] <== preimage;
    signal hashOutput <== hasher.out;

    hash === hashOutput;
}

component main = hashProof();

/* INPUT = {
    "preimage": "12345",
    "hash": "4267533774488295900887461483015112262021273608761099826938271132511348470966"
} */
````

## Study

### Symmetric vs Asymmetric Encryption (AES, RSA)

**🤔 Consider the following:**
1. What is the primary difference between symmetric and asymmetric encryption?

The primary difference between symmetric and asymmetric encryption lies in how the use keys for encryption and decryption.
-	Symmetric Encryption: 
    -	Single Key: Uses the same key for both encryption and decryption.
    -	Speed: Generally faster and more efficient for encrypting large amounts of data.
    -	Key Distribution: The key must be kept secret and shared securely between the communicating parties. If the key is compromised, so is the encrypted data.
    -	Example Algorithms: AES (Advanced Encryption Standard), DES (Data Encryption Standard).


-	Asymmetric Encryption: 
    -	Key Pair: Uses a pair of keys – a public key for encryption and a private key for decryption.
    -	Security: Enhances security since the private key does not need to be shared; only the public key is distributed. Only the corresponding private key can decrypt data encrypted with the public key.
    -	Speed: Generally slower than symmetric encryption and often used for smaller amounts of data or for securely exchanging symmetric keys.
    -	Example Algorithms: RSA (Rivest-Shamir-Adleman), ECC (Elliptic Curve Cryptography).
    In practice, asymmetric encryption is often used to exchange symmetric keys securely, which are then used for the actual data encryption.



2. Can you briefly explain how AES (Advanced Encryption Standard) works?

AES (Advanced Encryption Standard) is a widely used symmetric encryption algorithm.
1.	Key Length: AES supports three key lengths: 128, 192, or 256 bits. The length of the key affects the security and the number of rounds of processing the data undergoes.
2.	  Block Size: AES encrypts data in fixed-size blocks of 128 bits (16 bytes).
3.	  Rounds: AES performs multiple rounds of encryption, depending on the key length:
1.	10 rounds for a 128-bit key
2.	12 rounds for a 192-bit key
3.	14 rounds for a 256-bit key
4.	  Steps in Each Round:
•	SubBytes: Each byte of the block is replaced with a corresponding byte from a fixed substitution table (S-box).
•	ShiftRows: Rows of the block are shifted cyclically to the left.
•	MixColumns: Columns of the block are mixed to provide diffusion, which helps spread the influence of each bit over many parts of the block.
•	AddRoundKey: The block is XORed with a round key derived from the original key.
5.	 Final Round: The final round omits the MixColumns step but includes the SubBytes, ShiftRows, and AddRoundKey steps.
The combination of these operations ensures that the encrypted output is highly secure and resistant to various types of cryptographic attacks.



3. What makes RSA a popular choice for public-key encryption? 

RSA (Rivest-Shamir-Adleman) is popular for public-key encryption due to several key attributes:
1.	Security: RSA's security relies on the mathematical difficulty of factoring large composite numbers. As of now, it is considered secure against known cryptographic attacks, especially with sufficiently large key sizes.
2.	Public and Private Key Pair: RSA uses a pair of keys—one public and one private. The public key can be freely distributed for encryption, while the private key is kept secret for decryption. This asymmetric approach simplifies key distribution compared to symmetric encryption methods.
3.	Versatility: RSA can be used for both encryption and digital signatures. This versatility makes it useful for various applications, such as securing communications and verifying the authenticity of messages.
4.	Standardization: RSA has been widely adopted and standardized by many organizations and protocols, such as SSL/TLS for securing web communications, contributing to its widespread use and trust in the security community.
5.	Ease of Integration: RSA is well-supported by many cryptographic libraries and systems, making it relatively easy to integrate into existing software and protocols.
6.	Established Research: RSA's mathematical foundation and security assumptions have been extensively researched and analyzed, providing a high level of confidence in its reliability.



### Hash Functions, Merkle Trees

#### Hash Functions

**🤔 Consider the following:**

1. What is a hash function and what are its primary uses in cryptography?

A hash function is a cryptographic algorithm that takes an input and produces a fixed-size string of bytes, known as a hash value or digest. Its primary uses in cryptography include ensuring data integrity by detecting any alterations in the data, generating digital signatures to authenticate and verify the integrity of messages or documents, and securely storing passwords by comparing hashed values rather than plain-text passwords. Hash functions are also crucial in blockchain technology for linking blocks of transactions, ensuring the security and consistency of the blockchain ledger. Additionally, they are used in various cryptographic protocols for secure communication and in random number generation for cryptographic processes.


2. How does the SHA-256 hashing algorithm function, in simple terms?

The SHA-256 hashing algorithm functions by taking an input message and processing it through a series of steps to produce a fixed 256-bit (32-byte) hash value. The process begins by padding the message to ensure its length is a multiple of 512 bits, then initializing eight fixed 32-bit words. The padded message is divided into 512-bit chunks, each chunk being processed in a series of 64 rounds involving bitwise operations, modular additions, and logical functions using constants and the current state. These operations mix the bits of the input in a way that even a small change in the input results in a drastically different output. The final output is a 256-bit hash value, unique to the original input, making it nearly impossible to reverse-engineer or find two different inputs with the same hash.


3. What is the Poseidon hash function and why is it particularly useful in ZKPs?

The Poseidon hash function is a special type of hash designed to be highly efficient for ZKPs. Unlike traditional hashes like SHA-256, Poseidon is optimized to work well with the mathematical operations used in ZKPs, making the process of creating and verifying proofs faster and less resource-intensive. Its design reduces the number of complex steps needed, which is crucial for the efficiency and scalability of cryptographic protocols that rely on ZKPs.


#### Merkle Trees

**🤔 Consider the following:**

1. Can you describe the structure of a Merkle tree?

A Merkle tree, or hash tree, is used in cryptography to ensure data integrity and consistency. It consists of leaf nodes at the bottom, each containing a hash of a data block. These leaf nodes are paired and hashed together to form parent nodes, which are then paired and hashed again, continuing this process up the tree until a single root hash is formed at the top. The root hash serves as a unique fingerprint for all the underlying data. This structure allows for efficient and secure verification of data integrity since only a small subset of the hashes, along with the root hash, are needed to verify the authenticity of any data block. 


2. How are Merkle trees used within the blockchain context?

In blockchain technology, Merkle trees ensure data integrity and efficiency by organizing transactions into a tree structure. Each transaction is hashed, and these hashes are paired and hashed together until a single root hash, called the Merkle root, is formed. This Merkle root is included in the block header, allowing efficient and secure verification of transactions with minimal data. Any alteration in the transactions changes the corresponding hashes and the Merkle root, making tampering easily detectable. This structure enhances blockchain scalability and security.


3. Why are Merkle trees useful for efficient and secure verification of large data structures?

Merkle trees are essential for efficient and secure verification of large data structures because they allow for the integrity of any data element to be verified using a small subset of hashes instead of the entire dataset. By organizing data into a hierarchical structure of hashes, any change in the data alters the corresponding hashes up to the root, making tampering easily detectable. This enables compact proofs, or Merkle proofs, which consist of a minimal set of hashes needed for verification. Additionally, Merkle trees scale efficiently, ensuring quick verification even for large datasets, and support decentralized verification, crucial for applications like blockchain.



### Digital Signatures (Schnorr)

**🤔 Consider the following:**

1. Can you describe what digital signatures are and why they are essential in digital communications?

Digital signatures are cryptographic tools that ensure the authenticity and integrity of digital communications. They use public key infrastructure to create a unique, encrypted code that is attached to a message or document, verifying the sender's identity and confirming that the content has not been altered during transmission. This process involves the sender's private key to generate the signature and the recipient's public key to verify it. Digital signatures are essential because they provide a secure way to authenticate the origin of messages and maintain data integrity, thereby preventing forgery, tampering, and impersonation in digital communications.


2. Explain the workings of the Digital Signature Algorithm (DSA).

The Digital Signature Algorithm (DSA) is a cryptographic method used to generate and verify digital signatures, ensuring the authenticity and integrity of digital messages. It involves key generation, where public parameters p, g, and g are established, and a private key x is selected. During signature creation, the sender hashes the message and uses a random number k to compute the signature components r and s. Verification involves the recipient using the sender’s public key and the message hash to confirm that the signature (r, s)  matches the computed value, thereby validating the message's origin and integrity. This process prevents tampering and ensures that the message has not been altered.


### DLP-based Public-Key Cryptography (DLP, DH, Elgamal)

**🤔 Consider the following:**

1. What is the Discrete Logarithm Problem (DLP)?

The discrete logarithm problem (DLP) is a fundamental challenge in the field of cryptography. It involves finding the exponent x in the equation gx ≡ h mod p, where g is a known base, h is a known result, and p is a prime number. The difficulty of solving this problem lies in the fact that, while it is straightforward to compute gx mod p given g, x, and p, the reverse process—determining x from g, h, and p—is computationally infeasible for large values of p. This one-way function property underpins the security of many cryptographic systems, such as Diffie-Hellman key exchange and various digital signature schemes.


2. How does the Diffie-Hellman protocol work?

The Diffie-Hellman protocol enables two parties to securely exchange a shared secret key over a public channel. Both parties agree on a large prime number p and a base g. Each party selects a private key, computes a public key using the base and prime, and exchanges these public keys. They then use the received public key and their own private key to compute the shared secret. This shared key is the same for both parties but remains secure because its computation relies on the discrete logarithm problem, which is difficult to reverse without the private keys.


3. What is the main idea behind ElGamal encryption?

ElGamal encryption is a public-key cryptosystem that secures messages using the discrete logarithm problem. It involves a recipient generating a private key and a public key based on a large prime number p and a base g. To encrypt a message, the sender chooses a random value, computes two values based on the recipient's public key, and sends these as ciphertext. The recipient decrypts the message using their private key by applying modular arithmetic to recover the original message. The system's security hinges on the difficulty of reversing the discrete logarithm problem.


4. Can you name a drawback of using DLP-based systems?

A key drawback of using discrete logarithm problem (DLP)-based systems is their vulnerability to advances in algorithmic techniques and computational power. As algorithms like the Number Field Sieve become more efficient and computing power increases, the time required to solve discrete logarithms decreases, potentially compromising the security of DLP-based cryptographic systems. Additionally, DLP-based systems often require larger key sizes compared to other cryptographic methods to maintain security, which can lead to increased computational overhead and performance issues.



### Cryptographic Commitments

**🤔 Consider the following:**

1. What is the main purpose of Cryptographic Commitments?

Cryptographic commitments are used to securely bind a value to a specific piece of data while keeping the data hidden until later. The primary purpose is to ensure that once a commitment is made, the original data cannot be altered without detection. This mechanism involves two main phases: the commitment phase, where the data is hidden and committed to using a cryptographic function, and the reveal phase, where the data is disclosed and verified against the commitment. This ensures data integrity and authenticity, making cryptographic commitments crucial in various applications like secure voting, blockchain transactions, and cryptographic proofs.


2. What type of information does a Polynomial Commitment hide and reveal?

A polynomial commitment hides the details of a polynomial function while allowing verification of specific properties about the polynomial. When a commitment is made, the actual polynomial is concealed, but a cryptographic commitment value is generated. This commitment value can later be used to prove that a given value or evaluation point is consistent with the polynomial without revealing the polynomial itself. Essentially, a polynomial commitment ensures that while the polynomial's specific form remains hidden, one can still validate claims about it, such as its value at certain points or its correctness, enabling secure interactions in cryptographic protocols.


3. How do Pedersen Commitments contribute to privacy in cryptography?

Pedersen Commitments enhance privacy in cryptography by allowing one to commit to a value while keeping it hidden and maintaining the ability to prove certain properties about the committed value without revealing it. They achieve this through a cryptographic scheme where a value is committed using a combination of secret values and random elements. The commitment is designed to be both hiding and binding: hiding means the committed value remains confidential, and binding ensures that once a commitment is made, the value cannot be changed without detection. This mechanism supports privacy in applications such as confidential transactions and secure voting by protecting sensitive information while enabling verification.


4. Why are Cryptographic Commitments important in blockchain technology?

Cryptographic commitments are crucial in blockchain technology because they ensure data integrity and enable secure, transparent operations without compromising privacy. By allowing participants to commit to values or transactions while keeping the details hidden, commitments facilitate the verification of data and the prevention of tampering. In blockchains, commitments are used for functions like validating transactions, supporting smart contracts, and enabling privacy-preserving mechanisms. They ensure that once data is recorded, it cannot be altered or disputed, which upholds the trustworthiness and immutability of the blockchain ledger.


# 💪 Exercises

1. **Symmetric vs. Asymmetric Encryption**: What are the key differences between symmetric and asymmetric encryption? Provide a practical use case for each.

Symmetric encryption uses the same key for both encryption and decryption, making it faster but requiring secure key exchange methods. Asymmetric encryption uses a pair of keys—a public key for encryption and a private key for decryption—allowing for easier key management but at a slower speed due to its complexity. A practical use case for symmetric encryption is encrypting data at rest, such as database encryption, due to its efficiency in handling large amounts of data. In contrast, asymmetric encryption is ideal for secure communication over untrusted networks, like SSL/TLS for secure web browsing, where it ensures the secure exchange of session keys.


2. **Public-Key Cryptography and Key Exchange Protocols**: How can the Diffie-Hellman protocol enhance security in a messaging application?

The Diffie-Hellman protocol enhances security in a messaging application by enabling secure key exchange over an insecure channel. It allows two parties to generate a shared secret key independently without transmitting the key itself. This shared key can then be used for symmetric encryption of the messages, ensuring that even if an attacker intercepts the communication, they cannot decrypt the messages without the private keys of the participants. By regularly generating new shared keys, the protocol ensures forward secrecy, meaning past communications remain secure even if long-term keys are compromised.


3. **Hash Functions**: What features make SHA-256 and Poseidon good hash functions for ensuring data integrity? Mention one unique advantage of Poseidon.

SHA-256 and Poseidon are both effective hash functions for ensuring data integrity. SHA-256, part of the SHA-2 family, is widely used for its robustness, resistance to collisions, and computational efficiency, making it ideal for a variety of security applications. Poseidon, designed specifically for use in cryptographic protocols, offers similar security features but is optimized for parallel computation, enhancing performance on modern hardware. A unique advantage of Poseidon is its support for variable-length inputs and its ability to maintain security even with large-scale computations, making it particularly suited for blockchain applications and other systems requiring high throughput and security.


4. **Merkle Trees**: Explain how Merkle trees can help verify data in a large database efficiently.

Merkle trees help verify data in a large database efficiently by structuring data into a tree of hash values, where each leaf node represents a hash of a data block and each non-leaf node represents the hash of its child nodes. To verify any data block, only the hashes along the path from the block to the root node are needed. This enables quick verification with logarithmic complexity relative to the size of the database, as only a small subset of the tree needs to be checked, ensuring data integrity without requiring a complete scan of the entire database. This method is particularly useful in blockchain technology and distributed systems where efficient and secure data verification is crucial.


5. **Cryptographic Commitments**: How can Pedersen Commitments be used in a blockchain protocol to maintain transaction privacy?

Pedersen Commitments can be used in a blockchain protocol to maintain transaction privacy by allowing users to commit to a value without revealing it, and later revealing the value along with a secret key to prove its correctness. In a blockchain context, this means that users can create commitments to transaction amounts or other data, ensuring that the values remain hidden until the transaction is confirmed. This technique supports privacy-preserving features, such as zero-knowledge proofs, by ensuring that the committed values are indistinguishable from one another, thereby enhancing privacy while maintaining the integrity and verifiability of transactions.


6. **Digital Signatures**: How can you verify the authenticity of a digitally signed document?

To verify the authenticity of a digitally signed document, you use the signer's public key to check the digital signature against the document's contents. First, you obtain the public key associated with the signer's private key. Then, you use this public key and a verification algorithm to compare the digital signature with a hash of the document. If the signature matches the hash, it confirms that the document was signed with the signer's private key and has not been altered, thereby verifying the document's authenticity and integrity.
