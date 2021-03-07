Public key encryption

## Def and Security

### Applications

* Session setup (get shared key)
* Non-interactive applications, e.g. email. (encrypt with Alice's pk, and send msg to her)

### Def

check previous notes

### Security

check previous notes

##### Relation to symmetric cipher security

For symmertric ciphers: one-time security !=> many-time security.

For public key encryption: one-time security => many-time security. (as the attacker can encrypt any msg by himself with pk)

##### Chosen ciphertext security

CCS: The standard notion of security for public key encryption.

For $b={0,1}$, def $EXP(b)$:

* CCA phase 1: Adv. submit $i$ $CT$s and get decrypted $PT$s.
* Challenge: Adv. submmit $m_0$ and $m_1$ (equal length), and get $c=E(pk, m_b)$.
* CCA phase 2: Adv. submit $i$ $CT$s (not $c$), and get $PT$s.

CCA secure: Adv. can't tell $c$ is from $m_0$ or $m_1$.

## Construction 1: Trapdoor Permutations

### Trapdoor functions (TDF)

##### Def

$X \to Y$: a triple of efficient algs. $(G, F, F^{-1})$
* $(sk, pk) \gets G()$
* $F(pk, \cdot): X \to Y$
* $F^{-1}(sk, \cdot): Y \to X$

##### Secure TDFs

$(G,F,F^{-1})$ is secure if $F(pk, \cdot)$ is a one-way func: can be evaluated, but cannot be inverted without $sk$.

##### Public-key encryption from TDFs

requirements:

* $(G, F, F^{-1})$: secure TDF $X \to Y$
* $(E_s, D_s)$: symmetric auth. encrytion
* $H: X \to K$ a hash func

$E(pk, m)$:

* rand. $x \gets X, y=F(pk, x)$
* $k=H(x), c=E_s(k, m)$
* output $(y,c)$

TDF only applied to rand. $x$. $PT$ is encrypted by *symmetric system*, using $k$ by $H(x)$.

$D(sk, (y, c))$:

* $x=F^{-1}(sk, y)$
* $k=H(x), m=D_s(k, c)$
* output $m$

$(y,c) = F(pk, x) || E_s(H(x), m)$

##### Security Theorem

If $(G, F, F^{-1})$ is a secure TDF, $(E_s, D_s)$ provides auth. enc. and $H: X \to Y$ is a random oracle
=> $(G,E,D)$ is CCA secure.

##### Incorrect use of a TDF

Never do (Many attacks for this):
* $E(pk, m): c=F(pk, m)$
* $D(sk, m): m=F^{-1}(sk, m)$

Why?
Deterministic (no randomness): cannot be semantically secure.

### RSA trapdoor permutation

##### Construction

$G()$

Step 1: choose rand. primes $p,q \approx 1024 \text{ bits}$, set $N=p*q$

Step 2: choose integers $e, d \mid ed = 1 \mod \varphi(N)$.
* *$e,d$ are relatively prime to $\varphi(N)$;*
* *$e,d$ are modular inverses of each other.*

Step 3: output $pk=(N, e)\text{, }sk=(N, d)$
* *$e$: encryption exponent*
* *$d$: decryption exponent*

$F(pk,x)$
$= RSA(x) = x^{e} \text{ in } Z_N$

$F^{-1}(sk, y)$
$= y^d \text{ in } Z_N = RSA(x)^d = x^{ed} = x^{k\varphi(N)+1} = (x^{\varphi(N)})^k*x = x$

##### The RSA assumption

RSA is one-way permutation

For all efficient alg. $A$:
$$Pr[ A(N,e,y)=y^{1/e}]<negligible$$

### RSA pub-key encryption

##### Construction (ISO standard)

$(E_s, D_s)$: symmetric enc. scheme providing auth. enc.
$H: Z_N \to K$ where $K$ is key space of $(E_s, D_s)$

$G() \text{: } pk=(N,e) \text{, } sk=(N,d)$

$E(pk,m)$
1. choose rand $x$ in $Z_N$
2. $y \gets RSA(x)=x^e \text{, } k \gets H(x)$
3. output $(y,E_s(k,m))$

$D(sk,(y,c))=D_s(H(RSA^{-1}(y)),c)$

##### Textbook RSA is insecure

Textbook RSA:
* Public key: $(N, e)$; Encryption: $c \gets m^e \text{ in } Z_N$
* Secret key: $(N, d)$; Decryption: $c^d \to m$

RSA is a trapdoor permutation, not an encryption scheme by itself.

##### Attack to textbook RSA

Use textbook RSA to exchange key $k \in \{0,1\}^{64}$: $c=k^e \text{ in } Z_N$

prob. $\approx 20\%$: $\exists k_1*k_2=k$ ($k_1, k_2<2^{34}$), then $c/k_1^e=k_2^e \text{ in } Z_N$

use meet in the middle attack: $\approx 2^{40} \ll 2^{64}$

### RSA in practice: PKCS1

The ISO standard way is not often used.

PKCS1: public key cryptography standard number one

##### Idea

Given a symmetric encryption key (e.g. AES)

1. Preprocessing. Expand the key to full modulo size: e.g. 2048 bits.
2. Apply RSA function to the expanded key.

##### How to preprocess: PKCS1 v1.5

PKCS1 mode 1: signature
PKCS1 mode 2: encryption

mode 2:
1. the expanded key = [02 || random pad || FF || msg]
*02 indicates mode 2*
*no FF in random pad*
2. Apply RSA.

widelly deployed, e.g. HTTPS

##### Attack on PKCS1 v1.5

*by Bleichenbacher in 1998*

Attacker can: send $c$ to web server and know if $RSA^{-1}(c)$ starts with $02$

Chosen-ciphertext attack, to decrypt $c$:
1. choose $r \in Z_N$, compute $c' \gets r^e*c = (r*PKCS1(m))^e$
2. send $c'$ and use response
3. repeat million times then get $m$

Why? By multiply $r^e$, $m$ is shifted left and lsb is revealed.

##### HTTPS defense

RFC 5246: Don't tell attack if PT doesn't begin with $02$, but use rand. $R$ as $PT$, the session will terminated in the end as server/client use different keys.

##### PKCS1 v2.0: OAEP

*OAEP: Optimal Asymmetric Encryption Padding*

Why 'Optimal'? The CT is RSA output length, but CT in ISO standard way is RSA output plus a symmetric cipher.

OAEP improvements: OAEP+, SAEP+

*Checking pad is crucial for all these schemes*

### Attacks

##### Is RSA a one-way permutation?

To invert RSA without $d$, attacker must compute $x$ from $c=x^e \mod N$.

Best known alg:
Step 1: factor $N$ (hard)
Step 2: computer e'th roots modulo $p$ and $q$ (easy)

##### Wrong way to improve RSA's performance

Don't use small private key $d$: private key $d$ can be found from $(N, e)$

Wiener'87: if $d<N^{0.25}$ then RSA is insecure. (check details)
BD'98: if $d<N^{0.292}$ then RSA is insecure.

### RSA in Practice

##### Performance improvement: low public exponent

minimum value: $e=3$
recommended: $e=65537=2^{16}+1$ (17 multiplications)

Asymmetry of RSA: fast enc. / much slower dec.

##### Key length

cipher key-size => RSA modulus size
80 bits => 1024 bits
128 bits => 3027 bits (everybody uses 2048 bits)
256 bits (AES) => 15360 bits

##### Implementation attacks

Mathematically correct implementation, but still vulnerable:

Timing attack: *Kocher'97*

Power attack: *Kocher'99*

Faults attack: A computer error during $c^d (\mod N)$ can expose $d$. (check how)
* Very significant: just one error is completely enough to expose $d$.
* When using Chinese remaindering to speed up RSA decryption.
* Defense (common in crypto libraries): verify output, 10% slowdown.

*Never never never implement RSA yourself, use standard libraries instead.*

##### RSA key generation trouble

Don't generate keys immediately after start up, but make sure to get enought entropy.

Lesson: make sure generator is properly seeded.

## Construction 2: ElGamal (from Diffie-Hellman protocol)

### Convert DH protocol to pub-key enc.

##### The ElGamal system (a modern view)

$G$ finite cyclic group of order $n$ (e.g. $Z_p^*$, elliptic curve group)
$(E_s,D_s)$ symmetric auth. enc. defined over $(K,M,C)$
$H: G^2 \to K$ a hash function

The pub-key enc. system $(G,E,D)$

Key generation $G$:
* choose rand. generator $g$ in $G$ and rand $a$ in $Z_n$
* output $sk=a \text{, } pk=(g,h=g^a)$

$E(pk=(g,h),m)$
* get $\text{rand. } b \gets Z_n \text{, } u \gets g^b \text{, } v \gets h^b=g^{ab}$
* get $k \gets H(u,v) \text{, } c \gets E_s(k,m)$
* output $(u,c)$

$D(sk=a,(u,c))$
* get $v \gets u^a=g^{ab}$
* get $k \gets H(u,v) \text{, } m \gets D_s(k,c)$
* output $m$

##### ElGamal performance

$E$: two exponentiations, $u \gets g^b$ and $v \gets h^b$
$D$: one exponentiations, $v \gets u^a$

Generally, $D$ is 2x faster than $E$.

But if the recipient is always the same, then $pk=(g,h)$ is fixed, by pre-computing $[g^{(2^i)}, h^{(2^i)} \text{ for } i=1,...,log_2(n) ]$, the encryption could be faster than decryption.

### ElGamal security

Goal: chosen ciphertext attack secure

##### Computational Diffie-Hellman assumption

$G$: finite cyclic group of order n

Computational Diffie-Hellman (CDH) holds in $G$ if: $g, g^a, g^b$ !=> $g^{ab}$

##### Hash Diffie-Hellman assumption (stronger than CDH)

$G$: finite cyclic group of order n, $H: G^2 \to K$ a hash func.

Def: Hash-DH (HDH) assumption holds for $(G,H)$ if:
$$(g,g^a,g^b,H(g^b,g^{ab})) \approx_p (g,g^a,g^b,R)$$
$$\text{where } g \gets \text{generators of } G, (a,b) \gets Z_n, R \gets K$$

*indistinguishable from a truely independent random key*

##### Interactive Diffie-Hellman assumption

The adv. can make queries: send $(u_1,v_1) \in G^2$ to chal. and get $1$ if $(u_1)^a=v_1$ and $0$ otherwise.

IDH holds in $G$ if: $\forall$ efficient $A$, $Pr[A \text{ output } g^{ab}] < \text{ negligible }$

##### Chosen ciphertext security

Thm: if IDH holds in the group $G$, $(E_s,D_s)$ provides auth. enc. and $H:G^2 \to K$ is a random oracle, then ElGamal is CCA secure.

### ElGamal variants with a better security

##### CDH (instead of IDH)

CCA security based on CDH ($g,g^a,g^b$ !=> $g^{ab}$)

Option 1: use $G$ where CDH = IDH (a.k.a bilinear group)
Option 2: change the ElGamal system

##### CCA secure with CDH: Twin ElGamal

idea:
* $a_1,a_2 \gets Z_n$, $pk=(g,h1=g^{a_1},h2=g^{a_2})$, $sk=(a_1, a_2)$
* $E$: $k \gets H(g^b, (h_1)^b, (h_2)^b)$
* $D$: $k \gets H(u, u^{a_1}, u^{a_2})$

cost: one more exponentiation during enc/dec

##### CCA secure without random oracles

Option 1: use Hash-DH assumption in "bilinear groups" (e.g. elliptic curve)
Option 2: use Decision-DH assumption in any group.

## General principle for RSA and DH protocol

pub-key encryption made possible by one-way functions with special properties

### One-way functions

a func $f \text{: } X \to Y$ is one-way if:
* There is an efficient alg. to evaluate $f(\cdot)$
* Inverting $f$ is hard

##### PRG

Lemma: $f$ a secure PRG => $f$ is one-way

No special properties, difficult to use for key exchange.

##### The discrete log one-way function

$f(x)=g^x \in G$

Lemma: Discrete log hard in $G$ => $f$ is one-way

Properties: $f(x+y)=f(x)*f(y) \in G$
=> key-exchange and public-key encryption

##### RSA one-way function

$f(x)=x^e \text{ in } \mathbb{Z}_N$

Lemma: $f$ is one-way under the RSA assumption

Properties: $f(x*y)=f(x)*f(y)$ and $f$ has a trapdoor

*The trapdoor makes it very very easy to build digital signatures from RSA*