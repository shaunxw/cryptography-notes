#### MAC from Merkle-Damgard Hash Function

Attempt #1: $S(k, m) = H(k || m)$. Insecure because of extension attack.



HMAC construction:

$H$: hash function, for instance SHA-256.

$\text{HMAC}: S(k, m) = H(k \oplus \text{opad}, H(k \oplus \text{ipad || m}))$.

* Both $\text{opad}$ and $\text{ipad}$ are 512 bits.



HMAC properties

HMAC is assumed to be a secure PRF

* Can be proved under certain PRF assumption about compression function $h$.
* Security bounds similar to NMAC.
  * $q \ll |T|^{1/2}$



In TLS: must support HMAC-SHA1-96

* HMAC build on SHA1 and truncated to 96 bits.
* It's fine to use SHA1 here, though SHA1 is not C.R.



#### Timing Attacks on MAC Verification

Verification function:

```python
def verify(key, msg, sig):
    return HMAC(key, msg) == sig
```

The problem: `==` implemented as a byte-by-byte comparison, and returns false when first inequality found.



Lesson: do not implement crypto yourself!

