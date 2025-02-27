These are the four elements of secure communications:

- **Data Integrity** - Guarantees that the message was not altered. Any changes to data in transit will be detected. Integrity is ensured by implementing either of the Secure Hash Algorithms (SHA-2 or SHA-3). The MD5 message digest algorithm is still widely in use, however it is inherently insecure and creates vulnerabilities in a network. The use of MD5 should be avoided.
- **Origin Authentication** - Guarantees that the message is not a forgery and does actually come from whom it states. Many modern networks ensure authentication with algorithms such as hash-based message authentication code (HMAC).
- **Data Confidentiality** - Guarantees that only authorized users can read the message. If the message is intercepted, it cannot be deciphered within a reasonable amount of time. Data confidentiality is implemented using symmetric and asymmetric encryption algorithms.
- **Data Non-Repudiation** - Guarantees that the sender cannot repudiate, or refute, the validity of a message sent. Nonrepudiation relies on the fact that only the sender has the unique characteristics or signature for how that message is treated.

Hashing is based on a one-way mathematical function that is relatively easy to compute, but significantly harder to reverse.

There are four well-known hash functions:
- **MD5 with 128-bit digest** - Developed by Ron Rivest and used in a variety of internet applications, MD5 is a one-way function that produces a 128-bit hashed message. MD5 is considered to be a legacy algorithm and should be avoided and used only when no better alternatives are available. It is recommended that SHA-2 or SHA-3 be used instead.
- **SHA-1** - Developed by the U.S. National Security Agency (NSA) in 1995. It is very similar to the MD5 hash functions. Several versions exist. SHA-1 creates a 160-bit hashed message and is slightly slower than MD5. SHA-1 has known flaws and is a legacy algorithm.
- **SHA-2** - Developed by the NSA. It includes SHA-224 (224 bit), SHA-256 (256 bit), SHA-384 (384 bit), and SHA-512 (512 bit). If you are using SHA-2, then the SHA-256, SHA-384, and SHA-512 algorithms should be used whenever possible.
- **SHA-3** - SHA-3 is the newest hashing algorithm and was introduced by NIST as an alternative and eventual replacement for the SHA-2 family of hashing algorithms. SHA-3 includes SHA3-224 (224 bit), SHA3-256 (256 bit), SHA3-384 (384 bit), and SHA3-512 (512 bit). The SHA-3 family are next-generation algorithms and should be used whenever possible.

## Data Confidentiality
There are two classes of encryption used to provide data confidentiality; asymmetric and symmetric. These two classes differ in how they use keys.

###### symmetric
 Examples of stream ciphers include RC4 and A5 which is used to encrypt GSM cell phone communications.

Common block ciphers include DES with a 64-bit block size and AES with a 128-bit block size.

###### asymmetric
Asymmetric algorithms, also called public-key algorithms,  are designed so that the key that is used for encryption is different from the key that is used for decryption

Asymmetric algorithms use a public key and a private key. Both keys are capable of the encryption process, but the complementary paired key is required for decryption.

Asymmetric encryption can use key lengths between 512 to 4,096 bits. Key lengths greater than or equal to 2,048 bits can be trusted, while key lengths of 1,024 or shorter are considered insufficient.

Examples of protocols that use asymmetric key algorithms include:
- **Internet Key Exchange (IKE)** - This is a fundamental component of IPsec VPNs.
- **Secure Socket Layer (SSL)** - This is now implemented as IETF standard Transport Layer Security (TLS).
- **Secure Shell (SSH)** - This protocol provides a secure remote access connection to network devices.
- **Pretty Good Privacy (PGP)** - This computer program provides cryptographic privacy and authentication. It is often used to increase the security of email communications.

Common examples of asymmetric encryption algorithms are described in the table.

|**Asymmetric Encryption Algorithm**|**Key Length**|**Description**|
|---|---|---|
|**Diffie-Hellman (DH)**|512, 1024, 2048, 3072, 4096|The Diffie-Hellman algorithm allows two parties to agree on a key that they can use to encrypt messages they want to send to each other. The security of this algorithm depends on the assumption that it is easy to raise a number to a certain power, but difficult to compute which power was used given the number and the outcome.|
|**Digital Signature Standard (DSS) and Digital Signature Algorithm (DSA)**|512 - 1024|DSS specifies DSA as the algorithm for digital signatures. DSA is a public key algorithm based on the ElGamal signature scheme. Signature creation speed is similar to RSA, but is 10 to 40 times slower for verification.|
|**Rivest, Shamir, and Adleman encryption algorithms (RSA)**|512 to 2048|RSA is for public-key cryptography that is based on the current difficulty of factoring very large numbers. It is the first algorithm known to be suitable for signing, as well as encryption. It is widely used in electronic commerce protocols and is believed to be secure given sufficiently long keys and the use of up-to-date implementations.|
|**EIGamal**|512 - 1024|An asymmetric key encryption algorithm for public-key cryptography which is based on the Diffie-Hellman key agreement. A disadvantage of the ElGamal system is that the encrypted message becomes very big, about twice the size of the original message and for this reason it is only used for small messages such as secret keys.|
|**Elliptic curve techniques**|224 or higher|Elliptic curve cryptography can be used to adapt many cryptographic algorithms, such as Diffie-Hellman or ElGamal. The main advantage of elliptic curve cryptography is that the keys can be much smaller.|

###### Diffie Hellman
Diffie-Hellman (DH) is an asymmetric mathematical algorithm that allows two computers to generate an identical shared secret without having communicated before.

Here are two examples of instances when DH is commonly used:

- Data is exchanged using an IPsec VPN
- SSH data is exchanged

To help illustrate how DH operates, refer to the figure.

![[Pasted image 20250227174941.png]]

## Public Key Cryptography
There are three Digital Signature Standard (DSS) algorithms that are used for generating and verifying digital signatures:
- **Digital Signature Algorithm (DSA)** - DSA is the original standard for generating public and private key pairs, and for generating and verifying digital signatures.
- **Rivest-Shamir Adelman Algorithm (RSA)** - RSA is an asymmetric algorithm that is commonly used for generating and verifying digital signatures.
- **Elliptic Curve Digital Signature Algorithm (ECDSA)** - ECDSA is a newer variant of DSA and provides digital signature authentication and non-repudiation with the added benefits of computational efficiency, small signature sizes, and minimal bandwidth.

## Authorities and the PKI Trust System
Many vendors provide CA servers as a managed service or as an end-user product. Some of these vendors include Symantec Group (VeriSign), Comodo, Go Daddy Group, GlobalSign, and DigiCert among others.

 The class number is determined by how rigorous the procedure was that verified the identity of the holder when the certificate was issued. The higher the class number, the more trusted the certificate. Therefore, a class 5 certificate is trusted much more than a lower-class certificate.

|**Class**|**Description**|
|---|---|
|0|Used for testing in situations in which no checks have been performed.|
|1|Used by individuals who require verification of email.|
|2|Used by organizations for which proof of identity is required.|
|3|Used for servers and software signing. Independent verification and checking of identity and authority is done by the certificate authority.|
|4|Used for online business transactions between companies.|
|5|Used for private organizations or government security.|

Here are two of the most common methods of revocation:
- **Certificate Revocation List (CRL)** - A list of revoked certificate serial numbers that have been invalidated because they expired. PKI entities regularly poll the CRL repository to receive the current CRL.
- **Online Certificate Status Protocol (OCSP)** - An internet protocol used to query an OCSP server for the revocation status of an X.509 digital certificate. Revocation information is immediately pushed to an online database.


