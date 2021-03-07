#### Scenario

A single source key(SK) is sampled from

* Hardware random number generator
* A key exchange protocol

Need many keys to secure session:

* Unidirectional keys. (one for client to server, the other for server to client)
* Multiple keys for nonce-based CBC.

Goal: generate many keys from a source key.

Use a KDF (key derivation function) to achieve this.



#### KDF when source key is uniform

$F$: a PRF with key space $K$ and outputs in $\{0, 1\}^n$

Suppose source key $sk$ is uniform in $K$, define Key Derivation Function (KDF) as

$\text{KDF}(sk, ctx, L) := F(sk, (ctx || 0)) || F(sk, (ctx||1)) || ... || F(sk, (ctx || L))$

where the $ctx$ is a string that uniquely identifies the application (like SSH, web server).



#### KDF when source key is not uniform

PRFs are pseudo random only when key is uniform in $K$.

But source key often not uniformly random:

* Key exchange protocol: key uniform in some subset of $K$.
* Hardware RNG: may produce biased output.



##### Extract-then-Expand paradigm

Step 1: **extract** pseudo-random key $k$ from source key $sk$.

* Computational extractor: generate distribution that is indistinguishable from uniform.
* The extractor also takes a salt as parameter. Salt: a fixed non-secret string chosen *at random*.

Step 2: **expand** $k​$ by using it as a PRF key as before.



HKDF: a KDF from HMAC. It implements the extract-then-expand paradigm:

* Extract: use $k \leftarrow \text{HMAC}(salt, sk)$
  * The $salt$ is used as HMAC key, and $sk$ as HMAC data.
* Then expand using HMAC as a PRF with key $K$.



##### Password-Based KDF

Deriving keys from passwords:

* Do not use HKDF: passwords have insufficient entropy (only around 20 bits).
* Derived keys will be vulnerable to dictionary attacks.

PBKDF defenses: salt and a slow hash function.

Standard approach: PKCS#5 (PBKDF2)

* $H^{(c)}(pwd || salt)$: iterate hash funciton $c$ times to make it a slow hash function.
* The slow hash function slows down dictionary attacks.