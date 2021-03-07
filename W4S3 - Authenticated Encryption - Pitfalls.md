Case Study TLS v1.2 ...

* TLS can defend against replay attacks.



Bugs in older versions (prior to TLS v1.1)

* IV for CBC is preditable (chained IV).
* Padding oracle.
  * Defense: always check the MAC, no matter padding is valid or not.
  * Lesson: when decryption fails, do not explain why, or attackers will learn about PT.



Broken 802.11b WEP

* Previously discussed: two time pad and releated PRG seeds.
* The CRC checksum doesn't provide integrity: CRC is linear. (Use MAC instead)



CBC padding attacks:

1. E-then-M would completely avoid this problem.
   * MAC is checked first.
2. M-then-CBC provides A.E., but padding oracle destroys it.
   * M-then-CTR is secure as counter mode does not use padding.



Attacks on non-atomic decryptions: SSH binary packet protocol

* Don't use non-atomic decryption, stick to standard like GCM.
* Don't use any decrypted field before it is authenticated.



