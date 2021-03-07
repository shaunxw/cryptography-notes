#### Recap

Confidentiality

* Semantic security against a CPA attack.
* Encryption secure against *eavesdropping only*.

Integrity

* Existential unforgeability under a chosen msg attack.
* CBC-MAC, HMAC, PMAC, CW-MAC.



#### Why Authenticated Encryption?

CPA security cannot guarantee secrecy under active attacks.

* Example: for CBC with randomized IV mode, XOR the IV could tamper the data without being noticed.

* CPA-secure encryption should *never be used on its own*.



Only use one of two modes:

* If msg needs integrity but no confidentiality, use a **MAC**.
* If msg needs both integrity and confidentiality, use **authenticated encryption** modes.



#### Definition

$(E, D)$ is a cipher where

as usual $E: K \times M \times N \to C​$ where $N​$ is the optional nounce

but $D: K \times C \times N \to M \cup \{\perp\}​$ where $\perp \not\in M​$. If $D​$ outputs $\perp​$, the ciphertext is rejected.



Cipher $(E, D)$ provides **authenticated encryption(AE)** if it is

1. Semantic security under a CPA attack, and

2. Ciphertext integrity: attacker cannot create new cipher texts that decrypt properly.



Implications of AE:

1. Attacker cannot fool bob into thinking a msg was from Alice.
2. Security against chosen cipher text attacks.



Limitations of AE:

* Does not prevent replay attacks.
* Does not account for side channels(timing).



#### Chosen Ciphertext Attacks

Adversary's power: both CPA and CCA

* Can obtain the encryption of arbitraty messages of his choice.
* Can decrypt any ciphertext of his choice, other than challenge.(conservative modeling of real life)

Adversary's goal: break sematic security.



Thm: Let $(E, D)$ be a cipher that provides AE, then $(E, D)$ is CCA secure.

