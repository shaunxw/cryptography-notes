#### Definition

Goal: integrity, no confidentiality.



Def: Message Authentication Code (MAC) $I=(S, V)$, defined over $(K, M, T)$

* MAC *signing* algo., to generate a tag: $\text{tag} \leftarrow S(k,m)$
* MAC *verification* algo., to verify tag: $V(k, m, \text{tag}) = \text{`yes` or `no`}$
* The consistency requirement: $\forall k \in K, \forall m \in M, V(k, m, S(k, m)) = \text{`yes`}$.



Integrity requires a secret key $k$.

* A common mistake is that there is no shared key.
* CRC is to detect *random* transmission errors, not malicious errors.



#### Chosen Message Attack

Attacker's power: chosen message attack

* for $m_1, m_2, ... , m_q$ attacker is given $t_i \leftarrow S(k, m_i)$

Attacker's goal: existential forgery

* produce some *new* valid message/tag pair $(m, t)$



Secure MAC 

* Attacker cannot produce a valid tag for a new message.
* Given $(m, t)$, attacker cannot even produce $(m, t')$ for $t' \ne t$.



#### MACs Based on PRFs

For a PRF: $F: K \times X \to Y$, define a MAC $I_F = (S, V)$ as

* $S(k, m) := F(k, m)$

* $V(k, m, t)$: outputs $\text{`yes`}$ if $t = F(k, m)$ and $\text{`no`}$ otherwise.



Thm: If $F: K \times X \to Y$ is a secure PRF, and $1 / |Y|$ is negligible, then $I_F$ is a secure MAC.

* $I_F$ is secure as long as $|Y|$ is large, say $|Y| = 2^{80}$.



Example. AES: a MAC for 16-byte messages.



##### McDonald Problem

Main question for MACs based on PRFs: How to convert Small-MAC into a Big-MAC?



Two main constructions used in practice:

* CBC-MAC (used in banking)
* HMAC (used in internet protocols)



##### Truncating MACs based on PRFs

If $(S, V)$ is a MAC based on a secure PRF outputing n-bit tags, the truncated MAC outputing $w$ bits is secure, as long as $1/2^w$ is still negligible (say $w \ge 64$).