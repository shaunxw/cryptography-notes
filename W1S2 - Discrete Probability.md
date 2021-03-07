[See also: High School Mathematics Extensions](https://en.wikibooks.org/wiki/High_School_Mathematics_Extensions/Discrete_Probability)



U: finite set (e.g. $U = \{0, 1\}^n$)

Def: **Probability distribution** $P$ over $U$ is a function $P: U \to [0, 1]$ such that $\sum_{x \in U} P(x) = 1$



Examples:

1. Uniform distribution: for all $x \in U: P(x) = 1/|U|$ ($|U|$ is the size of $U$)

2. Point distribution at $x_0$: $P(x_0) = 1, \forall x \ne x_0: P(x) = 0$


Distribution vector: $(P(000), P(001), P(010), ... , P(111))$



#### Events

* For a set $A \subseteq U: Pr[A] = \sum_{x \in A} P(x)$    $\in  [0, 1]$.
  * Note: $Pr[U] = 1$

* The set A is called an **event**.



The union bound

* For events $A_1$ and $A_2$, $Pr[A_1 \cup A_2] \le Pr[A_1] + Pr[A_2]$ (because they could intersect each other) 
  * If $A_1 \cap A_2 = \emptyset$, $Pr[A_1 \cup A_2] = Pr[A_1] + Pr[A_2]$



#### Random Variables

Def: a random variable $X$ is a function $X: U \to V$

Example: $X: \{0, 1\}^n \to \{0, 1\}$; $X(y) = lsb(y) \in \{0, 1\}$. For the uniform distribution on $U$, $Pr[X = 0] = 1/2$, $Pr[X = 1] = 1/2$

More generally:

* rand. var. $X$ induces a distribution on $V$: $Pr[X = v] := Pr[X^{-1}(v)]$

* Or human language:

  1. A random variable defines two parts: 1. A set $V$ containing all possible values; 2. The distribution on $V$.

  2. The random variable $X$ takes value in $V$, and induces a distribution on $V$.

##### The uniform random variable

Let $U$ be some set, e.g. $U = \{0, 1\}^n$

We write  $r \xleftarrow{R} U$ to denote a **uniform random variable** sampled over $U$

for all $a \in U: Pr[r = a] = 1/|U|$

(formally, $r$ is the identity function: $r(x) = x$ for all $x \in U$



#### Randomized Algorithms

* Deterministic algorithm
  * $y \leftarrow A(m)$. The same input always get the same output.
* Randomized algorithm
  * $y \leftarrow A(m; r)$ where $r \xleftarrow{R} \{0, 1\}^n$. It has an *implicit* argument called $r$, where $r$ is sampled a new *uniformly at random every time* the algorithm is run.
  * For the same input $m$, the algorithm out changes every time you run it.
  * The output of randomized algorithm is a random variable $y \xleftarrow{R} A(m)$, which defines a distribution over the set of all possible outputs of this algorithm, given the input $m$.
  * Example, a encryption algo.: $A(m; k) = E(k, m), y \leftarrow A(m)$



#### Independence

Def: events $A$ and $B$ are independent if $Pr[A \text{ and } B] = Pr[A] \cdot Pr[B]$.

* "$A$ and $B$" means they happen at the same time.

* Two events $A$ and $B$ are independent of one another if one event happens tells you nothing if the other happened or not.

Def: random variables $X$, $Y$ taking values in $V$ are **independent** if $\forall a, b \in V: Pr[X=a \text{ and } Y=b] = Pr[X=a] \cdot Pr[Y=b]$

* Even you know $X=a$, that tells you nothing about the value of $Y$.



#### XOR

XOR of two strings in $\{0, 1\}^n$is their bit-wise addtion mod​ 2.

##### An import property of XOR

Thm: $Y$ a rand. var. over $\{0, 1\}^n$ (arbitrary distribution), $X$ an indep. uniform var. on $\{0, 1\}^n$, then $Z := Y \oplus X$ is uniform var. on $\{0, 1\}^n$.

(A classical joke: the only thing cryptographers know how to do is just XOR things together.)



#### The birthday paradox

Let $r_1, ... , r_n \in U$ be indep. identically distributed random vars.

Thm: When $n = 1.2 \cdot |U|^{1/2}$ then $Pr[\exists i \ne j: r_i = r_j] \ge 1/2$.

* Example: Let $U = \{0, 1\}^{128}$, after sampling about $2^{64}$ random messages from $U$, some two sampled messages will likely be the same.
* Birthday: If $24$ ($=1.2 \cdot \sqrt{365}$ ) people get together in the room, it's likely 2 of them have the same birthday. (Fun fact: People's birthday is not distrubuted uniformly, there is a bias towards December.)

