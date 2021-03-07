### PRF Switching Lemma

Any secure PRP is also a secure PRF, if $|X|$ is sufficiently large.

Lemma: Let $E$ be a PRP over $(K, X)$, then for any q-query adversary $A$:

$|\text{Adv}_{\text{PRF}}[A, E] - \text{Adv}_{\text{PRP}}[A, E]| < q^2 / (2 \cdot |X|)$



### Using PRPs and PRFs: One-time Key

Goal: build "secure" encryption from a secure PRP. (e.g. AES)

One-time key security:

* Adversary's power: Adv. sees only one ciphertext.(one-time key)

* Adversary's goal: Learn info about PT from CT (break sematic security)



##### Incorrect use of a PRP

Electronic Code Book(ECB) Mode: break PT into blocks, each as big as the block cipher block.

* Problem: if $m_1=m_2$, then $c_1=c_2$.
* ECB is not sematically secure for messages that contain more than one block.
  * The adversary use two msg in the EXP, one with two different blocks, and the other with two same blocks. The adv. is 1.



##### Secure Construction: Deterministic Counter Mode

Deterministic counter mode from a PRF $F$:

$E_{\text{DETCTR}}(k, m) = m[0] \oplus F(k, 0) || m[1] \oplus F(k, 1) || ... || m[L] \oplus F(k, L)$

* Acutally a stream cipher build from a PRF(e.g. AES, 3DES)
* $\text{Adv}_{\text{SS}}[A, E_{\text{DETCTR}}] = 2 \cdot \text{Adv}_{\text{PRF}}[B, F]$



### Using PRPs and PRFs: Many-time keys

Example: file system, networking.

##### Security for Many-time Keys

Key used more than once implies the adversary sees many CTs with same key.

Adversary's power: chosen-plaintext attack (CPA)

* Can obtain the encryption of arbitrary messages of his choice (conservative modeling of real life)

Adversary's goal: break sematic security.



CPA security. For a given cipher $(E, D)$, defined over $(K, M, C)$, and for a given adversary $A$, we define *Experiment 0* and *Experiment 1*. For $b = 0, 1$

* The challenger select $k \xleftarrow{R} K$
  * The same key is used for all encryptions.
* The adversary submits a sequence of queries to the challenger.
  * For $i=1, 2, ...$, The $i$th query is a pair of messages, $m_{i0}, m_{i1} \in M$, of the same length. The challenger computes $c_i \xleftarrow{R} E(k, m_{ib})$, and send $c_i$ to adversary.
    * Why called CPA? If the adversary want CT of $E(k, m)$, it queries with $m_{j0}=m_{j1}=m$.
* The adversary outputs a bit $b \in \{0, 1\}$.

For $b=0, 1$, let $W_b$ be the event that $A$ outputs 1 in Experiment $b$, We define $A$'s advantage with respect to $(E, D)$ as $\text{CPA}adv[A, (E, D)] := |Pr[W_0] - Pr[W_1]|$.

A cipher is called CPA secure, if for all "efficient" adversaries $A$, the value $\text{CPA}adv[A, (E, D)]$ is negligible.



One-time pad, deterministic counter mode, etc, are insure under CPA.

* $E(k, m)$ always outputs the same CT for msg $m$.
* An attacker can learn that two encrypted files are the same, two encrypted packets are the same, etc.
* Lesson: if secret key is to be used multiple times, given the same plaintext message twice, encryption must produce different outputs.



Solutions

* Randomized encryption.
  * A message $m$ isn't mapped to one CT, but to a set of CTs. Every time encrypted, a CT is selected randomly from the set.
  * Encrypting the same msg twice gives different CT with high probability.
  * CT must be longer than PT. (CT-size = PT-size + "number of random bits")
    * If PT-size is small, then overhead is too big.
* Nonce-based encryption.
  * An additional input $n$ called nonce: $c = E(k, m, n), m = D(k, c, n)$.
  * Nonce $n$ is a *public* value that changes from msg to msg. $(k, n)$ pair *never* used more than once.
    * Nonce doesn't need to be secret or random, it only needs to be unique.
    * Method 1: nonce is a *counter*. *Stateful* (maintain the nonce).
    * Method 2: encryptor chooses a *random* nonce $n \leftarrow N$, assuming $N$ is sufficiently enough that $n$ doesn't repeat in a key lifetime. *Stateless*.
    * System should be secure when nonces are chosen by the adversary.



##### Modes of Operation: Cipher Block Chaining(CBC) Mode

Construction 1: CBC with random Initialization Vector(IV)

* Let $(E, D)$ be a PRP. $E_{\text{CBC}}(k, m)$: choose *random* $\text{IV} \in X$ and do:
  * $c[0] = E(k, \text{IV} \oplus m[0])$
  * $c[1]=E(k, c[0] \oplus m[1]), c[2] = E(k, c[1] \oplus m[2]), ...$
  * And CT is $\text{IV} || C[0] || C[1] ||...$

* Decryption
  * $m[0] = D(k, c[0]) \oplus \text{IV}$
  * $m[1] = D(k, c[1]) \oplus c[0], m[2] = D(k, c[2]) \oplus c[1], ...$
  * And PT is $m[0] || m[1] || ...$

* CBC theorem: $\text{Adv}_{\text{CPA}}[A, E_{\text{CBC}}] \le 2 \cdot \text{Adv}_{\text{PRP}}[B, E] + 2q^2L^2/|X|$
  * Note: CBC is only secure as long as $q^2L^2 \ll |X|$.
  * In real world, $q$ is the number of messages encrypted with $k$, and $L$ is the max length of any one message (by the number of blocks). The thm tells how long frequently to replace the key.
  * Suppose we want the advantage $\le 1/2^{32}$, which means $q^2L^2/|X| < 1/2^{32}$
    * AES-128. $|X| = 2^{128}$, then $qL < 2^{48}$. So after $2^{48}$ AES blocks, must change key.
    * 3DES:  $|X|=2^{64}$, then $qL<2^{16}$. After 1/2 MB, must change key.
* Warning: CBC where attacker can *predict* the IV is not CPA-secure.
  * Check the book for how to attack.
  * Very common mistake. Bug in SSL/TLS v1.1.
  * It crucial that IV is *random*.



Construction 1': nonce-based CBC.

* Use a non-random but unique nonce to get IV.
  * CT is the same length with PT.
* Two independent key $(k, k_1)$ needed: $k$ is used to encrypt message blocks, and $k_1$ is used to encrypt the nonce to get a random IV.
  * Encryption and decryption is same with random IV mode, except the step to get IV.
  * $\text{IV} = E(k_1, \text{nonce})$
  * Encrypting the nonce with $k_1$ is crucial.
    * Without it this step and use nocne as IV, it would be a predictable IV.
    * If $k_1 = k$, would also lead to attacks.
  * Easily misused API in OpenSSL: IV is a parameter, the user is responsible to make sure it's random.



Padding: If the last message block is shorter than the block length of AES, a pad will be added to it.

* The pad would be removed during decryption.
* TLS: for $n>0$, $n$ byte pad is $[n, n, ... , n]$; if no pad needed, add a dummy block.
  * CBC with ciphertext stealing avoids the dummy block.



##### Modes of Operation: Randomized Counter(CTR) Mode

Randomized counter mode is superior to CBC mode.



Construction 2: Rand CTR mode.

* Rand CTR mode does not need a block cipher, but a secure PRF is enough - $F: K \times \{0, 1\}^n \to \{0, 1\}^n$.
* Choose a *random* IV, and start counting from this IV.
  * $c[0] = F(k, \text{IV}) \oplus m[0], c[1] = F(k, \text{IV} + 1) \oplus m[1], ... , c[L] = F(k, \text{IV} + L)  \oplus m[L]$
  * IV is included in CT. (longer than PT)
  * A new IV is chosen at random everytime encrypting a message. Encrypting the same message twice will result different CTs.
* Rand CTR mode is completely parallelizable. (unlike CBC)



Construction 2': Nonce CTR mode.

* To ensure $F(k, x)$ is never used more than once, choose IV as:
  * The higher 64-bits is the nonce.
  * The lower 64-bits is the counter, which starts at 0 for every message.
* Encrypt at most $2^{64}​$ blocks using a particular nonce.



CPA analysis of rand CTR mode

* The advantange is $\text{Adv}_{\text{CPA}}[A, E_{\text{CTR}}] \le 2 \cdot \text{Adv}_{\text{PRP}}[B, E] + 2q^2L/|X|$
* CTR mode is only secure as long as $q^2L \ll |X|$. Better than CBC.
* Suppose we want the advantage $\le 1/2^{32}$, then $q^2L/|X| < 1/2^{32}$
  * AES: $|X| = 2^{128}$, then $qL^{1/2} < 2^{48} $. So after $2^{32}$ CTs each of len $2^{32}$, must change key (total of $2^{64}$ AES blocks).



##### CTR v.s. CBC

For every single aspect, CTR mode is superior to CBC. Most modern encryption schemes are moving to CTR mode and abandoning CBC.

|                           |       CBC        |            CTR |
| :------------------------: | :--------------: | -------------: |
| Uses                      |       PRP        |            PRF |
| Parallel processing       |        No        |            YES |
| Security of rand. enc.    | $q^2L^2 \ll |X|$ | $q^2L \ll |X|$ |
| Dummy padding block       |       Yes        |             No |
| 1 byte msgs (nonce-based) |  16x expansion   |   no expansion |



These modes provides security against eavesdropping, but none of them ensures data integrity. *Always using these modes in addition to an integrity mechanism.*

