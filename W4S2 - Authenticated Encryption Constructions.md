#### History

AE is introduced in 2000. [KY'00, BN'00]

Crypto libs before then provides separate APIs for CPA-secure encryption and MAC.

Every project had to combine the two itself but not all combinations provide AE.



#### AE Theorems

Let $(E, D)$ be CPA-secure cipher and $(S, V)$ secure MAC. Then:

1. **Encryption-then-MAC**: always provides AE.
2. **MAC-then-Encryption**: may be insecure against CCA attacks.
   * When $(E, D)$ is rand-CTR mode or rand-CBC, **M-then-E** provides AE.
   * For rand-CTR mode, one-time MAC is sufficient. (MAC here doesn't need to be full secure, and then we can use faster MAC)



Recommendation: *aways use E-then-M*.



#### AE Standards

**GCM**: CTR mode encryption then CW-MAC. (accelerated via Intel's PCLMULQDQ instruction)

**CCM**: CBC-MAC then CTR mode encryption (802.11i). Adv: all based on AES, could be implemented in litttle amout of code.

**EAX**: CTR mode encryption then CMAC.



All of the above modes are nonce-based.

* The (nonce, key) pair should never ever repeat.
* Nonce doesnot need to be random, it's okay to use a counter as nonce.



All support AEAD (authenticated encryption with associated data). Only part of the msg is intended to be encrypted, but the whole message is instended to be authenticated.

* Example: for IP packets, header is not encrypted, in order to let router to route them, but payload is encrypted.



If no MAC security, then E-then-M would not have ciphertext integrity.

* MAC Security: given msg and tag pair, the attack can't produce another valid tag. 
* All MACs in above modes provides MAC security.



OCB mode: a direct construction from a PRP.

* Not a standard due to patents.



Comparision for modes (Crypto++ 5.6.0):

| Cipher  | Code Size | Speed MB/sec (AMD Opteron 2.2GHz) |
| :------ | :-------: | :-------------------------------- |
| AES-GCM |   Large   | 108                               |
| AES-CCM |  Smaller  | 61                                |
| AES-EAX |  Smaller  | 61                                |

AES-OCB speed: 129MB/sec.



Which AE standard to choose: Only use these three modes. AES-GCM is prefered.