AES is a substitution-permutation network, instead of Feistel.

* In Feistel network, *half* of the bits got changed in every round.
* In Subs-Perm network, *all* bits are changed in every round.



#### Generic Subs-Perm Network

For each round:

1. XOR the current state with round key $k_i$.
2. Substitution layer, invertible (S-box in Feistel is not).
3. Permutation layer, invertible.



#### AES-128 Schematic

##### Key Expansion

128-bits key = 16 bytes, expanded to 10*16 bytes.

##### 10 Subs-Perm Rounds

For each round except the last one:

1. XOR the input and round key.
   * The input is 126-bits = 16 bytes = 4*4 bytes matrix.
2. Perform invertible Subs-Pub functions on current state:
   1. `ByteSub`
   2. `ShiftRow`
   3. `MixColumn`
3. The last round has no `MixColumn` step.

##### The Round Functions

`Bytesub` applies the S-box to every byte in the current state.

* $\forall i, j: A[i, j] = S[A[i, j]]$ 
* The substution table is easily computable and then doesn't need to be hard coded.

`ShiftRow`: Items in row $i$ got shifted by $i$ positions.

`MixColumns`: Apply a linear transformantion to each of the columns.

##### Code Size/Performance Tradeoff

|                                           | Code Size |                     Performance |
| ----------------------------------------- | :-------: | ------------------------------: |
| Pre-compute round functions (24KB or 4KB) |  largest  | fastest: table lookups and xors |
| Pre-compute S-box only (256 bytes)        |  smaller  |                          slower |
| No pre-computation                        | smallest  |                         slowest |

The server could send codes without no pre-computed tables, and the client pre-compute tables before encryption.

Hardware: AES instructions in Intel/AMD CPUs, called AES-NI. Fast!



#### Attacks On AES

Best key recovery attack: four times better than exhaustive search [BKR '11].

* AES-128 key lengh is seen as 126-bits.

Related key attack on AES-256: [BK '09]

* A weakness in the key expansion design of AES.

* Given $2^{99}$ input/output pairs from *four related keys* (most bits are the same) in AES-256 can recover keys in time $\approx 2^{99}$.
  * In practice, choose keys in random.