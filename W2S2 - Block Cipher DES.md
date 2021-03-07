#### The Data Encryption Standard(DES) History

* Early 1970: Horst Feistel designs Lucifer at IBM.
  * Key and block lenght: 128 bits.
* 1973: NBS asks for block cipher proposal. IBM submits variant of Lucifer.
* 1976: NBS adopts DES as a federal standard.
  * Key-len = 56 bits; block-len = 64 bits.
* 1997: DES broken by exhaustive search.
* 2000: NIST adopts Rijndael as AES to replace DES.



#### DES Core Idea - Feistel Network

Given functions $f_1, ... , f_n: \{0, 1\}^n \to \{0, 1\}^n$

Goal: build a invertible function $F: \{0, 1\}^{2n} \to \{0, 1\}^{2n}$

Feistel network in symbol:

$L_i = R_{i-1}; R_i = f_i(R_{i-1}) \oplus L_{i-1} \text{  (}i = 1, ..., d\text{)}$

* $L_i$ is the left n-bits; $R_i$ is the right n-bits.



Claim: for all $f_1, ... , f_d: \{0, 1\}^n \to \{0, 1\}^n$, Feistel network $F: \{0, 1\}^{2n} \to \{0, 1\}^{2n}$ is invertible.

* $R_i = L_{i+1}; L_i=f_{i+1}(L_{i+1}) \oplus R_{i+1}$
* Inversion is basically the same circuit, with $f_1, ... , f_d$ applied in reverse order. (friendly for hardwares)
* Used in many block ciphers, but not AES.



##### Why Feistel network is awesome?

Thm (Luby-Rackoff '85)':

$f: K \times \{0, 1\}^n \to \{0, 1\}^n$ a secure PRF

=> 3-round Feistel $F: K^3 \times \{0, 1\}^{2n} \to \{0, 1\}^{2n}$ a secure PRP

* => secure block cipher
* Notice $K^3$, which means 3 *independent* keys needed.



##### DES construction: 16 round Feistel network

$f_1, ... , f_{16}: \{0, 1\}^{32} \to \{0, 1\}^{32}$, $f_i(x)=F(k_i, x)$

* Input/output block: 64 bits (32*2).
* All $f$ are derived from a single $F$ with different keys $k_i$, which are expanded from $k$.
* A 56 bits DES key is expanded into 16 round keys, each of them is 48 bits.
* To invert the cipher, use round keys in reverse order.
* $F$ construction: [The Feistel(F) Function](https://en.wikipedia.org/wiki/Data_Encryption_Standard#The_Feistel_(F)_function)
  * The S-boxes in $F$ is the only non-linear part of DES.
  * If the S-boxes is linear, DES won't be secure.
    * $\exist$ a fixed binary matrix $B$, s.t. $\text{DES}(k, m) = B \cdot [m, k_1, ..., k_{16}] = C$
    * Then: $\text{DES}(k, m_1) \oplus \text{DES}(k, m_2) \oplus \text{DES}(k, m_3) =\text{DES}(k, m_1 \oplus m_2 \oplus m_3)$. There will be an easy test to tell DES is not a random function.
  * Event S-boxes are close to linear, DES won't be secure.
    * If you choose S-boxes in random, they'll tend to be close to linear functions, and the key can be recovered after $\approx 2^{24}$ outputs. (BS '89)



#### Attacks on DES

##### Exhaustive Search for Block Cipher Key

Goal: given a few input/output pairs, find key $k$.

Lemma: Suppose DES is an *ideal cipher* ($2^{56}$ random invertible functions), then $\forall m, c$ there is at most *one* key such that $c=\text{DES}(k, m)$ with probability $\ge 1-1/256 \approx 99.5\%$.

* Proof: for a random key $k'$, the prob. of resulting the same $c$ is $\le 2^{64}$; for all possible keys, it's $\le 2^{56} \cdot 1/2^{64} = 1/2^8=1/256$.
* For two DES pairs, unicity prob. $\approx 1-1/2^{}71$; For AES-128, given two in/output pairs, unicity prob. $\approx 1 - 1/2^{128}$
* => Two input/output pairs are enough for exhaustive key search (check all keys one by one).



##### The DES Challenge

Issued by RSA. Goal: find $k \in \{0, 1\}^n$ s.t. $\text{DES}(k, m_i) = c_i$ for $i=1, 2, 3$.

DES is totally broken.



##### Strengthening DES against Exhaustive Search

Method 1: Triple-DES

* Define $\text{3E: } K^3 \times M \to M$ as $\text{3E}((k_1, k_2, k_3), m) = E(k_1, D(k_2, E(k_3, m)))$.
* Key size = 3 * 56 = 168 bits; 3x slower than DES (problem).
* Why not Double-DES? Meet-in-the-middle attacks.
  * Find $k_1, k_2$ s.t. $E(k_1, E(k_2, m)) = c$, which is $E(k_2, m)=D(k_1, c)$.
  * Step 1: build table for all keys $E(k_2, m)$; Step 2: try all the keys on $D(K_1, c)$.
  * Time: $2^{56}\log(2^{56}) + 2^{56}\log(2^{56})  < 2^{63} \ll 2^{112}$.
  * Same attack on 3DES: time = $2^{118}$.
  * *All similar construction can be attacked by meet-in-the-middle.*

Method 2: DESX

* Define ES as: $\text{EX}((k_1, k_2, k_3), m) = k_1 \oplus E(k_2, m \oplus k_3)$
  * Note $k_1 \oplus E(k_2, m)$ and $E(k_2, m \oplus k_1)$ does nothing.
* No performance penalty like 3DES.

* Against exhaustive search attack, but not against more subtle attacks.



#### More Attacks

Never ever design your own block cipher, just stick to standards like AES.



Side channel attacks

* Measure time to do enc/dec, measure power consumption for enc/dec.
* Looking at cache misses of the CPU core the algo. is running on.

Fault attacks

* Make the smart card to malfunction, by overclocking it, warming it up etc.
* Computing errors in the last round expose the secret key $k$.
  * Defense: check to make sure the result is correct before output it.

Linear and differential attacks

* Given many in/output pairs, can recover key in time less than $2^{56}$ (better than exhaustive search).
* Linear cryptanalysis: suppose for random $k, m$, $Pr[m[i_1] \oplus ... \oplus m[i_r] \oplus c[j_1] \oplus ... \oplus c[j_v] = k[l_1] \oplus ... \oplus k[l_u]] = 1/2 + \epsilon$
  * For some $\epsilon$. For DES, this exists with $\epsilon = 1/2^{21}$. (due to the 5th S-box is too close to linear) 
  * Subset of msg bits XOR subset of CT bits, if these two subsets are totally independent, the probability of the equation holds would be $1/2$.
  * Use the bias $\epsilon$. Thm: given $1/\epsilon^2$ random $(m, c=DES(k, m))$ pairs then $k[l_1, ... , l_u] = \text{MAJ}[m[i_1, ..., i_r] \oplus c[j_1, ..., j_v]]$ with prob. $\ge 97.7\%$.
    * For DES, roughly speaking, can find 14 key bits this way in time $2^{42}$, then brute force the remaining $56-14=42$ bits in time $2^{42}$. Total attack time $\approx 2^{43}$ with $2^{42}$ random input/output pairs.
* Lesson: a tiny bit linearity in S-box leads to attack. Do NOT design cipher yourself!

Quantum attacks

* Generic search problem: let $f: X \to \{0, 1\}$, find $x \in X$, s.t. $f(x) = 1$
  * For classical computer, time: $O(|X|)$
  * Quantum computer[Grover '96]: $O(|X|^{1/2})$
* DES: time $\approx 2^{28}$; AES-128: time $\approx 2^{64}$.
  * Defense: move to 256-bits key ciphers, e.g. AES-256.

