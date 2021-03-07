## What is Cryptography?

Crypto Core

1. Secret key establishment: At the end, Alice and Bob have a shared key, and confirmed each other's identity.
2. Secure communication: Confidentiality and intergrity.

More applications

1. Digital signature.
2. Anonymous communication.
3. Anonymous digital cash.

Protocols

* Eelection
* Private auctions.
* (More generally) Secure multi-party communication.
  * Theorem: Any computation that can be done with a trusted authority, can also be done without a trusted authority.

Crypto magic (hard to category)

* Privately outsourcing computation.
* Zero knowledge (proof of knowledge).



#### A Rigorous Science

The three steps in cryptography:

1. Precisely specify threat model.
2. Propose a construction.
3. Prove that breaking construction under *threat model* will solve an underlying hard problem.



## History of Cryptography

#### Symmetric Ciphers

Historic examples (all badly broken)

1. Substitution cipher.
   * Caesar cipher.
     * Can't be called a cipher, since there is no key.
     * Instead, fixed shift by 3.
   * If substitution table is chosed randomly, the size of key space is $26! \approx 2^{88}$(large enough).
   * To break it: using letter/diagram/triagram frequencies.
2. Vigenere cipher. Also breakable by frequencies.
3. Rotor machines.
   * Early example: the Hebern machine.
   * Most famous: the Enigma (3-5 rotors), rotors rolls in different frequencies.
4. Data Encryption Standard (1974)
   * DES: # keys = $2^{56}$, bock size = 64 bits. Small key space, breakble easily by brute force.
   * Today: AES(2001), Salsa20(2008), and others.