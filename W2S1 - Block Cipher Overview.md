### Block Cipher

A deterministic cipher $(E, D)$, both of which take a $k$ bits key and a $n$ bit plain text block as input,  the output is a $n$ bits cipher text block.

* Examples
  * 3DES: $n$ = 64 bits, $k$ = 168 bits.
  * AES: $n$ = 128 bits, $k$ = 128/192/256 bits.



Block ciphers are typically built by iteration.

1. It takes a key $k$, and expand it to $k_1, ... , k_n$ called round keys.
2. Iterately encrypt the message using round function.
   * $R(k, m)$ is called a round function.
   * Iterate $k_1$ to $k_n$, call $R(k_1, m), ... , R(k_n, \cdot)$, the final output is cipher text.
   * For 3DES, $n$ is 48; for AES, $n$ is 10.



Perfomance: block ciphers are considerably slower than stream ciphers. 



### Abstraction of Block Cipher: PRFs and PRPs

Pseudo Random Function(PRF) defined over $(K, X, Y)$:

$F: K \times X \to Y$

such that exists "efficient" algorithm to evaluete $F(k, x)$

* $K$: key space; $X$: input space; $Y$: output space.



Pseudo Random Permutation(PRP) defined over $(K, X)$:

$E: K \times X \to X$

such that:

1. Exists "efficient" *deterministic* algorithm to evaluate $E(k, x)$.
2. The function $E(k, \cdot)$ is *one-to-one*.
3. Exists "efficient" inversion algorithm $D(k, y)$.



* Example PRPs: 3DES, AES, ...
* Functionally, any PRP is also a PRF.
  * A PRP is a PRF where $X=Y$ and  is efficiently invertible.



##### Secure PRFs

Let $F: K \times X \to Y$ be a PRF.

$\text{Funs}[X, Y]$ is the set of *all* functions from $X$ to $Y$

and $S_F = \{ F(k, \cdot) \text{ s.t. } k \in K\} \subseteq \text{Funs}[X, Y]$

* Set $\text{Funs}[X, Y]$ size: $|Y|^{|X|} = 2^{128 \cdot 2^{128}}$
  * Functions in $\text{Funcs}[X, Y]$ are turely random functions.
* Size of the set $S_F$: $|K|$. For AES, it's $2^{128}$.
  * $S_F$ explaination: $F(k, \cdot)$ defines a function from $X$ to $Y$ where $k$ is *fixed*, and $S_F$ is the set of all such funcitons for all possible keys in the key space $K$.

*Intuition*: a PRF is **secure** if a random function(truly) in $\text{Funs}[X, Y]$ is indistinguishable from a random function(pseudo) in $S_F$.



##### An easy application: PRF => PRG

Let $F: K \times \{0, 1\}^n \to \{0, 1\}^n$ be a secure PRF.

Then the following $G: K \to \{0, 1\}^{nt}$ is a secure PRG:

$G(k) = F(k, 0) || F(k, 1) || ... || F(k, t)$.

* Called couter mode.
* Key property: parallelizable. (run different func on CPU cores at the same time)
* Why it's secure? Security from PRF property: $F(k, \cdot)$ is indistinguishable from random function $f(\cdot)$.
  * $f(\cdot)$ is a truely random function, which implies $f(0), ... , f(1)$ are all independent random values.