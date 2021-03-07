##### Build a PRF from a PRG

Let $G: K \to K^2$ be a secure PRG.

Define 1-bit PRF $F: K \times \{0, 1\} \to K$ as

$F(k, x \in \{0, 1\}) = G(k)[x]$

Thm: If $G$ is a secure PRG then $F$ is a secure PRF.



##### Extending a PRG

Let $G: K \to K^2$. Define $G_1: K \to K^4$ as $G_1(k) = G(G(k)[0]) || G(G(k)[1])$

Then we get a 2-bit PRF: $F(k, x \in \{0, 1\}^2) = G_1(k)[x]$



##### Extending to N: the GGM PRF (Goldreich, Goldwasser, and Micali)

Let $G: K \to K^2$. Define PRF: $F: K \times \{0, 1\}^n \to K$ as

For intput $x = x_0 x_1... x_{n-1} \in \{0, 1\}^n$ do:

$k_1 = G(k)[x_0], k_2 = G(k_1)(x_1), ... , k_n=G(k_{n-1})[x_{n-1}]$

* Security: $G$ a secure PRG => $F$ a secuer PRF on $\{0, 1\}^n$.
* Not used in practice due to performance.



Can we build a secure PRP from a secure PRG? Yes, plug the GGM PRF into Luby-Rackoff theorem.