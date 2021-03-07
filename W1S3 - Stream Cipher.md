### Symmetric Cipher Definition

Def: a **symmetric cipher** defined over $(K, M, C)$

is a pair of "efficient" algs $(E, D)$ where

$E: K \times M \to C$, $D: K \times C \to M$, s.t. $\forall m \in M, k \in K$: $D(k, E(k, m)) = m$.

* $K$: key space (the set of all possible keys); $M$: message space; $C$: cipher text space.
* $D(k, E(k, m)) = m$ is called the consistency equation.
* In theory, "efficient" means runs in polynomial time; In practice, it means concrete time constrains.
* $E$ is often randomized, $D$ is always deterministic.



### The One Time Pad

The first example of a "secure" cipher, by Vernam 1917:

$M = C = \{0, 1\}^n$, $K = \{0, 1\}^n$, key = (rand. bit string *as long as msg*)

$c := E(k, m) = k \oplus m$; $m = D(k, c) = k \oplus c$

* Indeed: $D(k, E(k, m)) = D(k, k \oplus m) = k \oplus (k \oplus m) = (k \oplus k) \oplus m = 0 \oplus m = m$

* Very fast enc/dec, difficult to use in practice: long keys, as long as plaintext.



### Information Theoretic Security

(Shannon 1949)

Basic idea: CT should reveal no "info" about PT.

Def: A cipher $(E, D)$ over $(K, M, C)$ has **perfect secrecy** if

$\forall m_0, m_1 \in M (|m_0| = |m_1|), \text{ and } \forall c \in C$

$Pr[E(k, m_0) = c] = Pr[E(k, m_1) = c]$, where $k \xleftarrow{R} K$

* Given CT, can't tell if msg is $m_0$ or $m_1$, for all $m_0$ / $m_1$.
* The most powerful adversary learns nothing about PT from CT.
* No CT only attack. (other attacks maybe possible)



Lemma: One time pad has perfect secrecy. Bad news.

Thm: Perfect secrecy requires $|K| > |M|$.



### Stream Cipher: Making OTP practical

Idea: replace "random" key by "pseudorandom" key, based on PRG.



PRG: is a func. $G: \{0, 1\}^s \to \{0, 1\}^n$ where $n \gg s$

* $\{0, 1\}^s$ is the seed space. The point is to expand short seeds to long keys.
* $G$ is "efficiently" computable by a deterministic algo, and has no randomness.
* The output $\{0, 1\}^n$ should *look* random.



How stream cipher works? Use $k$ as seed, expand it by $G(k)$

* $c = E(k, m) := m \oplus G(k)$, $D(k, c) := c \oplus G(k)$



Stream ciphers cannot have perfect secrecy.

* Since the key is shorter than the message.
* Security relies on PRG.



PRG must be unpredictable.

* Predictable means: $\exists i: G(k)|_{1, ..., i} \xrightarrow{algo.} G(k)|_{i+1, ..., n}$. Given the first $i$ bits of the output, there exists an "efficient" alog. that could compute the rest of it.
* We say $G: K \to \{0, 1\}^n$ is predictable if: $\exists$ "efficient" algo. $A$, and $\exists 1 \le i \le n-1$ s.t. $Pr[A(G(k))|_{1, ... , i} = G(k)|_{i+1}] \ge 1/2 + \epsilon$, for  some non-negl. $\epsilon$ ($\epsilon \ge 1/2^{30}$)
* Def: PRG is unpredictable if $\forall i$, no "eff" adv. can predict bit ($i$+1) for "non-neg" $\epsilon$.



Weak PRGs (NEVER use for crypto)

* LCG: Linear Congruential Generator.
* `random()` in glibc.



Negligible and non-negligible

* In practice, $\epsilon$ is a scalar and
  * $\epsilon$ non-neg: $\epsilon \ge 1/2^{30}$ (likely to happen over 1GB of data)
  * $\epsilon$ negligible: $\epsilon \le 1/2^{80}$ (won't happen over life of key)
* In theory, $\epsilon$ is a function $\epsilon: Z^{\ge 0} \to R^{\ge 0}$ and
  * $\epsilon$ non-neg: $\exist d: \epsilon(\lambda) \ge 1/\lambda^d$ infinitely often	($\epsilon \ge 1/poly$ for many $\lambda$)
    * Often bigger than one of that polynomial.
  * $\epsilon$ negligible: $\forall d, \lambda \ge \lambda_d: \epsilon(\lambda) \le 1/\lambda^d$	($\epsilon \le 1/poly$ for large $\lambda$)
    * Less than all polynomial.
  * Human language: negligible means less than 1/exponential, and non-neg means less than 1/polynomial.



### Attacks on Stream Cipher and OTP

Attack 1: Two time pad

* XOR two CT: $c_0 \oplus c_1 \to m_0 \oplus m_1$
* Enough redudancy in English and ASCII that: $m_0 \oplus m_1 \to m_0, m_1$
* Examples
  * Project Venona(1941-1946)
  * MS-PPTP: the client and server use the same pad.
  * 802.11b WEP
    * 24bit IV, keys repeat after 16M frames.
    * Related seed( $IV||k$ ) with RC4 as PRG, 4000 frames can recover seed $k$. (Not two time pad, but even worse)
  * Disk encryption.
* Summary: never use stream cipher key more than once!
  * Network traffic: negotiate new key for every session(e.g. TLS).
  * Disk encryption: typically do not use a stream cipher.



Attack 2: OTP and stream ciphers provide no integrity (malleable)

* Adv. could XOR CT with $p$, then decrypting CT will get $m \oplus p$.
* Modifications to CT are undetected and have *predictable* impact on PT.



### Real-World Stream Ciphers

Old example

* RC4 (a PRG)
  * Used in HTTPS and WEP
  * Weeknesses:
    * Bias in initial output: probability of 2nd bytes equal to zero is 2/256; Defend: ignore initial bytes.
    * Prob. of two zero byte sequence is $1/256^2 + 1/256^3$.
    * Related key attack.
* CSS(Content scrambling system), badly broken
  * Used to encrypt DVD movies.
  * Linear feedback shift register (LFSR). CSS/GSM/Bluetooth(E0) are all based on LFSR and badly broken.



Modern stream ciphers

* eStream Project (2008)
  * PRG: $\{0, 1\}^s \times R \to \{0, 1\}^n$, an extra parameter: nonce.
    * Nonce: a non-repeating value for a given key.
  * $E(k, m; r) = m \oplus PRG(k; r)$
    * The pair $(k, r)$ is never used more than once.
  * Salsa 20: $\{0, 1\}^{128 \text{ or } 256} \times \{0, 1\}^{64} \to \{0, 1\}^{n}$    (max n = $2^{73}$ bits)
    * $Salsa20(k; r) := H(k, (r, 0)) || H(k, (r, 1)) || ...$



### PRG Security Def

Let $G: K \to \{0, 1\}^n$ be a PRG.

Goal: define what it means that $[k \xleftarrow{R} K, \text{ output } G(k)]$ is "indistinguishable" from a truly uniform distribution.



Statistical tests is used to distinguish a pseudo-random string $G(s)$ from a truly ranodm string $s$.

Statistical tests on $\{0, 1\}^n$: an algo. $A$ s.t. $A(x)$ outputs "0"(When $A$ think the input is not random) or "1"(random). 



Def: **Advantage**

Let $G: K \to \{0, 1\}^n$ be a PRG and $A$ a statistical test on $\{0, 1\}^n$

Define: $Adv_{PRG}[A, G] := |Pr_{k \xleftarrow{R} K}[A(G(k)) = 1] - Pr_{r \xleftarrow{R} \{0, 1\}^n}[A(r) = 1]| \in [0, 1]$

Adv. close to 1: $A$ can distinguish $G$ from rand. ($A$ breaks $G$ with adv. x)

Adv. close to 0: $A$ cannot dist.



Crypto definition of secure PRGs

Def: We say that $G: K \to \{0, 1\}^n$ is a **secure PRG** if:

for all efficient statistical tests $A$, $Adv_{PRG}[A, G]$ is negligible.



Are there provably secure PRGs? Unknown. (Prove a PRG is secure implies $P \ne NP$)



Easy fact: A secure PRG is unpredictable.



Thm(Yao'82): An unpredictable PRG is secure.

* Let $G: K \to \{0, 1\}^n$ be PRG. If $\forall i \in \{0, ... , n-1\}$, PRG $G$ is unpredictable at position i, then $G$ is a secure PRG.
*  In english: If next-bit predictor cannot distinguish G from random then no statistical test can.
* Example: For a PRG $G$, if you can easily compute the first n/2 bits from the last n/2 bits, is $G$ predictable? Yes. (A efficient stat. test can be build => $G$ is not secure => $G$ is predictable.)



Computational Indistinguishability Definition

Let $P_1$ and $P_2$ be two distributions over $\{0, 1\}^n$

Def: $P_1$ and $P_2$ are computationally indistinguishable (denoted $P_1 \approx_p P_2$) if

for all efficient statistical tests $A$, $|Pr_{x \leftarrow P_1}[A(x) =1] - Pr_{x \leftarrow P_2}[A(x)=1]| < \text{negligible }$

* Example: a PRG is secure if $\{k \xleftarrow{R}K: G(k)\} \approx_p \text{uniform}(\{0, 1\}^n)$



### Secure PRG Implies Semitically Secure Stream Cipher

Perfect security is too strict for stream cipher(key length is small), we need another definition(sematic security).



#### Sematic Security Advantage

**Experiment** $b$: 

* The adversary computes $m_0, m_1 \in M$, of the same length, and sends them to the challenger.
* The challenger computes $k \xleftarrow{R} K$, $c \xleftarrow{R} E(k, m_b)$, and sends $c$ to the adversary.
* The adversary outputs a bit $b \in \{0, 1\}$. 

For $b = 0, 1$, let $W_b$ be the event that $A$ outputs 1 in Experiment $b$. We define $A$’s semantics ecurity advantage with respect to $E$ as: $SS_{adv}[A, E] := |Pr[W_0] - Pr[W_1]|$



#### Sematic Security Def

A cipher $E$ is sematically secure if for all efficient adversaries $A$, the value $SS_{adv}[A, E]$ is negligible.

* For all explicit $m_0, m_1 \in M$: $\{E(k, m_0)\} \approx_p \{E(k, m_1)\}$ (cannot distinguish these two distributions).
* In english: No efficient adversary can distinguish the encryption of $m_0$ from the encryption of $m_1$.



#### Stream Ciphers are Semantically Secure

Thm: $G: K \rightarrow \{0, 1\}^n$ is a secure PRG, then stream cipher $E$ derived from $G$ is semantically secure.