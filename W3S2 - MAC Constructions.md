Goal: given a PRF for short messages (like AES), construct a PRF for long messages.



#### ECBC-MAC and NMAC

##### Encrypted CBC-MAC (ECBC-MAC)

Let $F: K \times X \to X ​$ be a PRP, define a new PRF $F_{\text{ECBC}}: K^2 \times X^{\le L} \to X​$

* The raw CBC function is not a secure MAC.
* Commonly used as an AES-based MAC.
* Used in CCM encryption mode (in 802.11i).
* NIST standard called CMAC (a variant  of ECBC-MAC).



##### Nested MAC (NMAC)

Let $F: K \times X \to K​$ be a PRP, define a new PRF $F_{\text{NMAC}}: K^2 \times X^{\le L} \to K​$. Check construction details in the book.

* Note that $F$ outputs elements in $K$ space.
* The cascade function is not a secure MAC.
* Not usually with AES. Why? Need to change AES key on every block, requires re-computing AES key expansion.
* The basis for HMAC.



##### The Last Encryption Step

ECBC-MAC and NMAC won't be secure without the last encryption step. Check construction details in the book.

* $\text{cascade}(k, m)$ => $\text{cascade}(k, m || w)$ for any $w$.
* $\text{rawCBC}$ function is easily broken using a 1-chosen msg attack.



##### Analysis & the Bounds

After signing $|X|^{1/2}$ messages with ECBC-MAC (using the same key), 

or $|K|^{1/2}$ messages with NMAC,

the MACs become insecure (chosen message queries).



##### Padding

Why padding? Message length is not multiple of block-size.



#### PMAC - Parallel MAC

Let $F: K \times X \to X $ be a PRP, define a new PRF $F_{\text{PMAC}}: K^2 \times X^{\le L} \to X$

* $P(k, i)$ function is needed, where $i$ is block number.
* Padding simillar to CMAC.
* PMAC is secure as long as $qL \ll |X|^{1/2}$.



PMAC is incremental. If a part of the message changed, the tag can be updated by a small amount of computation.



#### CW-MAC

##### One-time MAC

Analog of one time pad. One key is used only for one message.

* Can be secure against all adversaries.
* Faster than PRF based MACs (as it's only designed for one time use).



##### Cater-Wegman MAC (CW-MAC)

Goal: One-time MAC to many-time MAC.

Idea: apply one-time MAC on the message, and then encrypt the result with a PRF.



Let $(S, V)​$ be a secure one-time MAC, and $F​$ be a secure PRF. Carter-Wegman MAC:

$\text{CW}((k_1, k_2), m) = (r, F(k_1, r) \oplus S(k_2, m))​$ for random $r \leftarrow \{0, 1\}^n​$.

* CW-MAC is fast: $F$ is slow but the input $r$ is short; $S$ is fast and the input $m$ is long.

* Thm: If $(S, V)$ is a secure one-time MAC and $F$ a secure PRF then $\text{CW}$ is a secure MAC outputting tags in $\{0, 1\}^{2n}$.
* CW-MAC is a randomised MAC, the nounce $r$ is chosed fresh everytime the MAC is computed.
* Not PRF, as a single msg could be be mapped into different tags. 