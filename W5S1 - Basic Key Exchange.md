Based on symmetric cipher: Merkle Puzzle.

* Quadratic gap between participant's and attacker's work.
* Quadratic is the best possible if we treat cipher as black box oracle.
* Not practical.



To achieve exponential gap, two construction: Diffie-Hellman and public-key encryption.



#### The Diffie-Hellman Protocol (informally)

##### Construction

Fix and public known:

* A large prime $p$ (e.g. 2048-bit)
* An integer $g$ in $\{1, … , p\}$

Steps:

1. Alice choose a random $a​$ in $\{1, … , p-1\}​$, computes $A \leftarrow g^a ( \mod p )​$ and send $A​$ to Bob.
2. Bob choose a random $b$ in $\{1, … , p-1\}$, computes $B \leftarrow g^b (\mod p)$ and send $B$ to Alice.
3. Use $k_{AB} = g^{ab} (\mod p)$.
   * For Alice, $k_{AB} = B^a (\mod p) $;
   * For Bob, $k_{AB} = A^b  (\mod p)$.



Non-interactive property.

* Alice/Bob/Charlie post $g^a​$, $g^b​$, $g^c​$ in public place like FB. They get shared key between any two of them without communicating.



##### Security

Eavesdropper sees: $p, g, A=g^a(\mod p), B = g^b(\mod p)$

How hard to get $g^{ab}​$ from $g^a, g^b​$?

* Best know algo (GNFS): runtime $\exp(\widetilde{O}\sqrt[3]{x})​$.
  * The original DH is not hard enough, because of the $\sqrt[3]{x}$.
* Improvement: use elliptic curve instead of $(\mod p)$ - ECDHE (Elliptic-curve Diffie–Hellman).

Insecure against man-in-the-middle attack.



#### Public Key Encryption

##### Definition

A public-key encryption system is a triple of algs. $(G, E, D)$

* $G$: randomized algo. outputs a key pair $(pk, sk)$.
* $E(pk, m)$: randomized algo. that takes $m \in M$ and outputs $c \in C$.
* $D(sk, c)$: deterministic algo. that takes $c \in C$ and outputs $m \in M$.

Consistency requirement

* $\forall (pk, sk)$ output by G, $\forall m \in M: D(sk, E(pk, m)) = m$.

Constructions rely on hard problems from *number theory* and *algebra*.



##### Semantic Security

$\text{EXP}(b)$

1. Chal. $(pk, sk) \leftarrow G()$, and send $pk$ to adv.
2. Adv. send $m_0$ and $m_1$ to chal.
3. Chal. $c \leftarrow E(pk, m_b)$ and send $c$ to adv.

Adv. don't need chosen plain text queries. (Adv. can encrypt any msg with $pk$.)

Definition: $(G, E, D)$ is semantic secure if adv. can't tell he is given encryption of $m_0$ or $m_1$.



##### Establishing a Shared Secret

Steps

1. Alice $(pk, sk) \leftarrow G()$, and send Bob msg $(\text{'Alice'}, pk)$.
2. Bob choose a random $k \in \{0, 1\}^{128}$,  and send Alice msg $(\text{'Bob'}, c \leftarrow E(pk, k))$
3. Alice get shared secret $k \leftarrow D(sk, c)$.

Difference with DH: inveractive.

* Even $pk$  posted in a public place, Bob still needs to generate $k$ and send $c$.

* DH: non-interactive property, no order to take.



##### Security

Adv. sees $pk$, $c \leftarrow E(pk, k)$ and wants $k \in M$.

Because of semantic security of public-key encryption, adv. cannot distinguish $\{ pk, E(pk, x), k\}$ from $\{pk, E(pk, x), \text{rand} \in M\}$.

Vulnerable to man-in-the-middle.

* Defense: digital signatures.