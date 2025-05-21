# Conceptual Quantum-Resistant Cryptography (PQC) Demonstration

This repository contains a simple, interactive web-based demonstration of the **workflow** of two fundamental types of Quantum-Resistant Cryptography (PQC) algorithms: a Key Encapsulation Mechanism (KEM) and a Digital Signature Scheme. These algorithms are designed to run on classical computers but are mathematically resistant to attacks from future quantum computers.

**Important Disclaimer:** This demo uses highly simplified, non-cryptographic operations for illustrative purposes only. It is **NOT** cryptographically secure and should **NEVER** be used for real-world encryption or signing. Its sole purpose is to help visualize the conceptual steps involved in PQC.

## Table of Contents

* [Introduction to PQC](#introduction-to-pqc)

* [Core Concepts Demonstrated](#core-concepts-demonstrated)

  * [Key Encapsulation Mechanism (KEM)](#key-encapsulation-mechanism-kem---for-confidentiality)

  * [Digital Signature Scheme](#digital-signature-scheme---for-authenticity--integrity)

* [How the Demo Works (Step-by-Step Logic)](#how-the-demo-works-step-by-step-logic)

  * [General Helpers](#general-helpers)

  * [KEM Logic](#kem-logic)

  * [Digital Signature Logic](#digital-signature-logic)

* [How to Use the Demo](#how-to-use-the-demo)

* [Technical Details](#technical-details)

## Introduction to PQC

Post-Quantum Cryptography (PQC), also known as Quantum-Resistant Cryptography, refers to cryptographic algorithms that are believed to be secure against attacks by both classical and quantum computers. Unlike Quantum Key Distribution (QKD), which relies on quantum physics and specialized hardware for key exchange, PQC algorithms are mathematical constructions designed to run on existing classical computing infrastructure. The development of PQC is crucial to protect current and future sensitive data from the threat of sufficiently powerful quantum computers.

## Core Concepts Demonstrated

This demo illustrates the operational flow of two key PQC primitives:

### Key Encapsulation Mechanism (KEM) - For Confidentiality

* **Purpose:** KEMs are used to securely establish a shared secret key between two parties (e.g., Alice and Bob) over an insecure communication channel. This shared secret can then be used as a symmetric key for encrypting and decrypting bulk data (e.g., using AES). PQC KEMs are designed to ensure that even a quantum computer cannot break the encapsulation and discover the shared secret.

* **Workflow:**

  1. **Key Generation:** Alice generates a public/private key pair (`PK_A`, `SK_A`). `PK_A` is made public, while `SK_A` remains secret.

  2. **Encapsulation (by Bob):** Bob generates a random session key. He then "encapsulates" this session key using Alice's `PK_A`, producing a `Ciphertext`. This `Ciphertext` is sent to Alice.

  3. **Decapsulation (by Alice):** Alice receives the `Ciphertext`. She uses her `SK_A` to "decapsulate" the `Ciphertext`, which should yield the *same* session key that Bob generated.

  4. **Shared Secret:** If successful, Alice and Bob now securely share the same session key, which they can use for symmetric encryption.

### Digital Signature Scheme - For Authenticity & Integrity

* **Purpose:** Digital signatures provide assurance of the authenticity (who sent the message) and integrity (that the message hasn't been altered) of a message. PQC signature schemes are designed so that a quantum computer cannot forge a signature or alter a signed message without detection.

* **Workflow:**

  1. **Key Generation:** Alice generates a public/private key pair for signing (`PK_Sig_A`, `SK_Sig_A`). `PK_Sig_A` is made public, while `SK_Sig_A` remains secret.

  2. **Signing (by Alice):** Alice takes a message and uses her `SK_Sig_A` to compute a `Signature` for that message.

  3. **Verification (by Bob):** Bob receives the message, the signature, and Alice's `PK_Sig_A`. He uses `PK_Sig_A` to verify that the `Signature` is valid for that specific `Message`. If verification passes, he knows the message came from Alice and has not been tampered with.

## How the Demo Works (Step-by-Step Logic)

The HTML and JavaScript code simulate the interactions between Alice and Bob for both KEM and Digital Signature processes.

### General Helpers

* `generateSimpleKey(length)`: A non-cryptographic function that generates a random string of a specified `length`. This simulates cryptographic keys for demonstration purposes.

* `simpleHash(str)`: A non-cryptographic function that computes a simple hash of a string. This simulates cryptographic hash functions.

* `showMessage(element, message, type)`: A utility to display status messages to the user in the UI, with different styles for success, warning, and error.

### KEM Logic

1. **"Generate Alice's KEM Keys" Button:**

   * **Action:** When clicked, Alice's conceptual public (`kemAlicePk`) and private (`kemAliceSk`) keys are generated as random strings.

   * **Display:** These keys are shown in the respective output boxes.

   * **Outcome:** Alice is ready to receive encapsulated keys.

2. **"Bob Encapsulate Key" Button:**

   * **Prerequisite:** Alice's KEM public key must be generated.

   * **Action:** Bob generates a random `kemBobSessionKey`. This key is then "encapsulated" using Alice's `kemAlicePk`. The encapsulation is simulated by `simpleHash(kemBobSessionKey + kemAlicePk)`, producing `kemCiphertext`.

   * **Display:** Bob's generated session key and the ciphertext are shown.

   * **Outcome:** Bob has created a ciphertext containing a shared secret, ready to be sent to Alice.

3. **"Alice Decapsulate Key" Button:**

   * **Prerequisite:** Alice's KEM keys must be generated, and Bob must have encapsulated a key.

   * **Action:** Alice attempts to "decapsulate" the `kemCiphertext` using her `kemAliceSk`. The demo simulates this by checking if a re-derived hash (`simpleHash(kemBobSessionKey + kemAlicePk)`) matches the `kemCiphertext`.

   * **Display:** If successful, Alice's `aliceDecapsulatedKeyDiv` shows the recovered session key.

   * **Outcome:** A message indicates whether the decapsulation was successful and if Alice and Bob now share the same session key.

### Digital Signature Logic

1. **"Generate Alice's Signature Keys" Button:**

   * **Action:** Alice's conceptual public (`sigAlicePk`) and private (`sigAliceSk`) keys for signing are generated as random strings.

   * **Display:** These keys are shown in the respective output boxes.

   * **Outcome:** Alice is ready to sign messages.

2. **"Alice Sign Message" Button:**

   * **Prerequisite:** Alice's signature keys must be generated, and a message must be entered in the `messageToSign` textarea.

   * **Action:** Alice "signs" the entered message using her `sigAliceSk`. The signing process is simulated by `simpleHash(message + sigAliceSk)`, producing `currentSignature`.

   * **Display:** The generated signature is shown.

   * **Outcome:** Alice has created a signature for her message.

3. **"Copy Alice's Sig Public Key to Bob" Button:**

   * **Prerequisite:** Alice's signature keys must be generated and a message signed.

   * **Action:** This button simulates sending Alice's `sigAlicePk`, the `messageToSign`, and the `generatedSignature` to Bob's side for verification. The values are copied to Bob's corresponding input fields.

   * **Outcome:** Bob has all the necessary information to attempt verification.

4. **"Bob Verify Signature" Button:**

   * **Prerequisite:** All fields on Bob's side (`messageToVerify`, `signatureToVerify`, `bobSigPublicKey`) must be populated.

   * **Action:** Bob attempts to "verify" the signature. In this simplified demo, verification passes if the `message`, `signature`, and `publicKey` on Bob's side exactly match the *original* values Alice generated. This illustrates that any tampering with the message or signature would cause verification to fail.

   * **Outcome:** A message indicates whether the signature was successfully verified (confirming authenticity and integrity) or if verification failed (indicating tampering or incorrect keys).

## How to Use the Demo

1. **Access the Canvas:** Open the "Conceptual Quantum-Resistant Cryptography (PQC) Demo" Canvas in your environment.

2. **Explore KEM:**

   * Click "**Generate Alice's KEM Keys**". Observe Alice's Public and Private Keys.

   * Click "**Bob Encapsulate Key**". Observe Bob's generated session key and the ciphertext.

   * Click "**Alice Decapsulate Key**". Observe Alice's decapsulated key and the success message.

3. **Explore Digital Signatures:**

   * Click "**Generate Alice's Signature Keys**". Observe Alice's Public and Private Keys.

   * Enter a message in the "Message to Sign" textarea.

   * Click "**Alice Sign Message**". Observe the generated signature.

   * Click "**Copy Alice's Sig Public Key to Bob**". This populates Bob's verification fields.

   * Click "**Bob Verify Signature**". Observe the verification result.

   * **Test Tampering:** Try changing the message or signature on Bob's side *before* clicking "Bob Verify Signature" to see verification fail.

## Technical Details

* **HTML5:** Provides the structural elements of the web page.

* **Tailwind CSS:** Used for responsive design and modern styling, ensuring a clean and intuitive user interface.

* **JavaScript:** Implements all the interactive logic for key generation, encapsulation, decapsulation, signing, and verification. It uses simple string operations and hashing for conceptual demonstration, not actual cryptographic primitives.
