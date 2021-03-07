#### Collision Resistance

Let $H: M \to T$ be a hash function where $|M| \gg |T|$.

A **collision** for $H$ is a pair $m_0, m_1 \in M$ such that $H(m_0) = H(m_1)$ and $m_0 \ne m_1$.

A function is **collision resistant** if for all (explicit) "efficient" algorithms A:

$\text{Adv}_{\text{CR}}[A, H] = Pr[\text{A outputs collision for H}]$ is negligible.



#### MACs from Collision Resistance

Let $I = (S, V)​$ be a MAC from short message over $(K, M, T)​$. Let $H: M^{big} \to M​$.

Def: $I^{\text{big}} = (S^{\text{big}}, V^{\text{big}})$ over $(K, M^{\text{big}}, T)$ as

$S^{\text{big}}(k, m) = S(k, H(m)); V^{\text{big}}(k, m, t) = V(k, H(m), t)$



Thm: If $I$ is a secure MAC and $H$ is collision resistant then $I^{\text{big}}$ is a secure MAC.

* Example: $S(k, m) = \text{AES}_{\text{2-block-cbc}}(k, \text{SHA-256}(m))$ is a secure MAC.



#### Generic Birthday Attack

Let $H: M \to \{0, 1\}^n$ be a hash function where $|M| \gg 2^n$.

Generic algorithm to find a collision in time $O(2^{n/2})$ hashes:

1. Choose $2^{n/2}$ random messages in $M: m_1, ... , m_{2^{n/2}}$ (distinct with hight probability).
2. For $i = 1, ... , 2^{n/2}$ compute $t_i = H(m_i) \in \{0, 1\}^n$.
3. Look for a collision $(t_i = t_j)$. If not found, got back to step 1.

Exptected number of iteration: 2. (according to birthday paradox)

Running time: $O(2^{n/2})$. Space: $O(2^{n/2})$.

##### The Birthday Paradox

Let $r_1, ... , r_n \in \{1, ..., B\}$ be independent identically distributed integers.

Thm: when $n = 1.2 \times B^{1/2}$ then $Pr[ \forall i \ne j: r_i = r_j ] \ge 1/2$.



#### Constructions

Goal: collision resistant (C.R.) hash functions.

* Step 1: given C.R. function for *short* messages, construct C.R. function for *long* messages.
* Step 2: Build C.R. function for short messages.



##### The Merkle-Damgard Iterated Construction

Given $h: T \times X \to T$ (compression function), we obtain $H: X^{\le L} \to T$ ($H_i$ - chainning variables).

* $H_1 = h(IV, m[0]), H_2 = h(H_1, m[1]), ... , t = H(i) = h(H_{i-1}, m[i])​$
* $IV$ is a fixed value and public.
* Padding block: `100..0 || msg_len`.

Thm: If $h​$ is collision resistant then so is $H​$.



##### Compression Function from a Block Cipher

$E: K \times \{0, 1\}^n \to \{0, 1\}^n​$ a block cipher.

The *Davies-Meyer* compression function: $h(H, m) = E(m, H) \oplus H$.

* Use message $m$ as key for the block cipher.

Thm: Suppose $E$ is an idea cipher (collection of $|K|$ random permutations.). Finding a collision $h(H, m) = h(H', m')$ takes $O(2^{n/2})$ evaluations of $(E, D)$.

* According to birthday paradox, $h$ is as collision resistant as possible.

There're other block cipher constructions. Note $E(m, H) \oplus m$ is not C.R.



##### Case study: SHA-256

* Merkle-Damgard function
* Davies-Meyer compression function
* Block cipher: SHACAL-2 (512 bits per block)