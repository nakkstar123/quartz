These provide constant round protocols as opposed to SPDZ. 

WRK's authenticated garbling. 
HSS's BMR style maliciously secure garbling for multiple parties. 

[WRK](https://eprint.iacr.org/2017/030.pdf) 
Previous works in the constant round setting use cut-and-choose and gate level cut-and-choose (LEGO) to achieve constant round but need to send over $\rho$ garbled circuits to achieve a statistical security of $2^{-\rho}$ which is optimal in this paradigm. There are also follow up works to bring amortized cost down. 
For AES and SHA evaluation, this work shows that single-execution overhead for malicious compared to semi-honest is just 8x and 16x respectively (3x and 9x amortized). 

BMR compiler is another way to get constant round protocols with malicious security. 

There are works like [[TinyOT]], SPDZ have round complexity linear in depth of the circuit. 

**This work achieves** $O(|\mathcal{C})$ function independent and function dependent communication and computation (symmetric key ciphertexts and operations), $|\mathcal{I}| + |\mathcal{O}|$ online communication and $O(|\mathcal{C})$ online comp / storage. 

The function-independent preprocessing phase calls $\mathcal{F}_{pre}$ that sets up correlated randomness between the two parties. Regardless of this, their protocol is extremely efficient in the other two phases i.e. only 2x over Yao's for function dependent preprocessing and 1x for online phase. 

They also develop an optimized version of [[TinyOT]] to instantiate $\mathcal{F}_{pre}$. Apparently, this work —both the idea of constructing an “authenticated” garbled circuit as well as the efficient TinyOT protocol we developed—is used in subsequent work [WRK17, HSSV17] on constant-round multiparty computation with malicious security.

It would probably be a good idea to first read the paper in the 2pc setting and then move on to the multiparty setting. 

## Protocol Overview
### Authenticated shares of garbled tables
#### Secret-sharing

$P_A$ holds a uniform global key $\Delta_A \in \{0,1\}^k$  and a bit $b$ known to $P_B$ is authenticated under $P_A$'s  key as follows: $P_A$ holds a key $K[b]$ and $P_B$ holds $M[b] = K[b]\oplus b\Delta_A$. Essentially $A$ and $B$ have uniform shares of $b\Delta_A$ when $b$ is known to $P_B$. This sharing is homomorphic as if the parties share $[b]_A$ (known to $A$ so secret shares of $b\times \Delta_B$) and $[c]_A$ then can compute $[b+c]_A$ by locally adding components. Intuitively, the security comes from the fact that: if either $P_A$ or $P_B$ want to cheat i.e. use an incorrect share to reconstruct an incorrect value, they can only do this is they also modify the MAC appropriately which amounts to them guessing the other party's global key. 
This can be **extended** to authenticated sharing of a secret bit known to *neither*. They hold $\lambda = r\oplus s$ where $P_A$ holds $(r, M[r], K[s])$ and $P_B$ holds $(s, M[s], K[r])$ which is also XOR-homomorphic (i.e if $\lambda_1, \lambda_2$ are secret shared this way, they can locally compute a sharing of $\lambda_1 + \lambda_2$).

The preprocessing functionality sets up these global keys where $\mathcal{A}$ can choose its key. Then parties request shares of authenticated bits, $\mathcal{A}$ can again choose its randomness. Also parties can send authenticated bits and the functionality provides fresh authenticated shares of the AND so the functionality is *stateful*. [How?] Does it check that the authenticated shares are valid? 
#### Garbling
Each wire $\alpha$ has a random mask $\lambda_\alpha \in \{0,1\}$ such that the circuit evaluator only observes $\hat{x} = x\oplus \lambda_\alpha$ when evaluating it. Obviously, the gate $G$ should then be garbled in a way such that the evaluator can use (labels corresponding to) $\hat{x}$ and $\hat{y}$ to decrypt (the label corresponding to) $\hat{z} = G(x, y)\oplus \lambda_\gamma$. 

So the garbler needs to decide this assignment of $\hat{z}$ based on values of $\hat{x}, \hat{y}, \lambda_{\alpha}, \lambda_{\beta}, \lambda_{\gamma}$. It suffices to do this for AND gates because XOR is free by setting labels to be $L_{\alpha,1} = L_{\alpha,0}\oplus\Delta$ for some fixed $\Delta$ that is known to $P_A$ but not to $P_B$. 

We can construct a garbled AND gate largely using this blueprint. We need to assign a truth table value (for the desired masked value) and garbled table value (to uncover the label corresponding to the desired masked value) for each $(\hat{x}, \hat{y})$ pair. 

$P_A$ knows that if $\hat{x} = 0, \hat{y} = 0$ then $x=\lambda_\alpha$ and $y=\lambda_\beta$ so $z=x\land y = \lambda_\alpha \land \lambda_\beta$ or $\hat{z} = (\lambda_\alpha \land \lambda_\beta) \oplus \lambda_\gamma$. This tells $P_A$ that in the $(\hat{x},\hat{y})=(0,0)$ position of the table, it should encrypt the masked value $\hat{z}$ and corresponding label $L_{\gamma,\hat{z}}$. Clearly, if $P_A$ is encrypting something in the $(\hat{x},\hat{y})$ position of the table, this should be decrypt-able using $L_{\alpha, \hat{x}}, L_{\beta, \hat{y}}$. This gives us the first position of the garbled table:

| $(\hat{x},\hat{y})$ | truth table                                                           | garbled table                                                                      |
| ------------------- | --------------------------------------------------------------------- | ---------------------------------------------------------------------------------- |
| (0,0)               | $\hat{z} =(\lambda_\alpha \land \lambda_\beta) \oplus \lambda_\gamma$ | $H(L_{\alpha, 0}, L_{\beta, 0}, \gamma, 00) \oplus (\hat{z}, L_{\gamma, \hat{z}})$ |
The other positions follow using the same logic. For example, if $\hat{x} = 0, \hat{y} = 1$ then $x=\lambda_\alpha, y = \overline{\lambda_\beta}$   so we calculate $\hat{z} = (\lambda_\alpha \land \overline{\lambda_\beta}) \oplus \lambda_\gamma$ and encrypt the label (and masked value) corresponding to this value instead. The labels used for encryption will also change because this is the situation where $(\hat{x},\hat{y})=(0,1)$. 

| $(\hat{x},\hat{y})$ | truth table                                                                           | garbled table                                                                                |
| ------------------- | ------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------- |
| (0,0)               | $\hat{z}_{00} =(\lambda_\alpha \land \lambda_\beta) \oplus \lambda_\gamma$            | $H(L_{\alpha, 0}, L_{\beta, 0}, \gamma, 00) \oplus (\hat{z}_{00}, L_{\gamma, \hat{z}_{00}})$ |
| (0,1)               | $\hat{z}_{01} =(\lambda_\alpha \land \overline{\lambda_\beta}) \oplus \lambda_\gamma$ | $H(L_{\alpha, 0}, L_{\beta, 1}, \gamma, 01) \oplus (\hat{z}_{01}, L_{\gamma, \hat{z}_{01}})$ |
Using the same logic we can also fill in the rest of the table:

| $(\hat{x},\hat{y})$ | truth table                                                                                     | garbled table                                                                                |
| ------------------- | ----------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------- |
| (0,0)               | $\hat{z}_{00} =(\lambda_\alpha \land \lambda_\beta) \oplus \lambda_\gamma$                      | $H(L_{\alpha, 0}, L_{\beta, 0}, \gamma, 00) \oplus (\hat{z}_{00}, L_{\gamma, \hat{z}_{00}})$ |
| (0,1)               | $\hat{z}_{01} =(\lambda_\alpha \land \overline{\lambda_\beta}) \oplus \lambda_\gamma$           | $H(L_{\alpha, 0}, L_{\beta, 1}, \gamma, 01) \oplus (\hat{z}_{01}, L_{\gamma, \hat{z}_{01}})$ |
| (1,0)               | $\hat{z}_{10} =(\overline{\lambda_\alpha} \land \lambda_\beta)\oplus \lambda_\gamma$            | $H(L_{\alpha, 1}, L_{\beta, 0}, \gamma, 10) \oplus (\hat{z}_{10}, L_{\gamma, \hat{z}_{10}})$ |
| (1,1)               | $\hat{z}_{11} =(\overline{\lambda_\alpha} \land \overline{\lambda_\beta)}\oplus \lambda_\gamma$ | $H(L_{\alpha, 1}, L_{\beta, 1}, \gamma, 11) \oplus (\hat{z}_{11}, L_{\gamma, \hat{z}_{11}})$ |
This process is inductive as the every time a wire is a "unlocked" by $P_B$ it also gets the label and (masked) value of the next label which it can then use for the next gate and so on. 

$P_B$ only sees the masked values which, without the random masks, are independent of the real values so this is secure against even against a malicious $B$ that can only decrypt the values it is supposed to. At the end, it has masked values, which can be unmasked by $P_A$ sending it the random masks only for the output wires (which are again independent from the random masks of all the other intermediate wires). 

The issue here is that $P_A$ can still be malicious by garbling the circuits incorrectly. For example, it can garble the $(0,0)$ label incorrectly in some gate and then if the evaluator aborts, it figures out the value of $\hat{x},\hat{y}$ and it knows $\lambda_\alpha, \lambda_\beta$ which leaks $x$ and $y$ which are intermediate wire values. The issue here is that $P_A$ knows the random masks so one idea for fixing this would be that the $\lambda$ values are hidden from both $P_A$ and $P_B$ and instead shared between them. This primarily raises two issues:
1) How will the garbler even construct the table correctly without knowing the masks?
2) Even if we find a way to do that in a way that maintains privacy from $P_A$, how can we ensure $P_A$ doesn't affect correctness by flipping something in its share (or rather, how can we detect and abort if it does)?
#### Shared authenticated garbling
We answer the first question by $P_A$ and $P_B$ holding additive shares of the garbled table where $P_B$'s shares are rather simple and this allows the construction to propagate once they obtain some preprocessed material (intentionally vague because it needs a clever trick). We address the issue of correctness by adding information theoretic MACs on these shares of the garbled tables. The idea makes sense but it might seem that if things are constructed in this "distributed fashion" there might not be an efficient way to realize this. 

In the table in [[#Garbling]] the encrypted value was $(\hat{z}, L_{\gamma, \hat{z}})$ which we now want to secret share between $P_A$ and $P_B$ as $r\oplus s = \hat{z}$ and $L_{\gamma, \hat{z}}^A \oplus L_{\gamma, \hat{z}}^B = L_{\gamma, \hat{z}}$. In this case, if $P_A$ flips bits and causes a selective abort, it still doesn't learn anything without $s$ which is held by $P_B$. But it could flip $r$ to mess up correctness, leading to computing some possibly different function or the same function on a different input. We fix this by authenticating $r$ under $P_B$'s global MAC key $\Delta_B$. The shares would now look something like:

| $(\hat{x},\hat{y})$ | $P_A$'s share of garbled table                                                       | $P_B$'s share of garbled table   |
| ------------------- | ------------------------------------------------------------------------------------ | -------------------------------- |
| (0,0)               | $H(L_{\alpha, 0}, L_{\beta, 0}, \gamma, 00) \oplus (r, M[r], L_{\gamma, \hat{z}}^A)$ | $(s,K[r],L_{\gamma, \hat{z}}^B)$ |
It isn't necessary to authenticate $s$ as $B$ can't do anything with it anyway. 

There a lot of questions left to answer. Starting with only authenticated random bit shares, how can we implement this kind of distributed construction of a secret shared garbled table? $P_A$ now doesn't know $\hat{z}$ so how would it pick the correct label? And how would it use **shares of** the masked output of one wire to obtain **shares of** the masked output of another wire? Finally, how would the authenticated shares propagate? How many do we need to get from $\mathcal{F}_{pre}$?

### Efficient Realization combining BDOZ shares and FreeXOR

The question is: with how little information can one (locally) compute authenticated shares of **masked output** and **corresponding labels**? 

As we will eventually see, this is the required preprocessing material:
- Global MAC keys $\Delta_A, \Delta_B$ used for authenticated bits. 
- Authenticated shares of secret shared $\lambda = r\oplus s$ (cf. [[#Secret-sharing]]) for each input wire and AND gate
- Reactively receives authenticated shared bits and returns authenticated share bit of the AND. (this happens for every AND gate in function dependent preprocessing)

Why is this enough? The optimizations are as follows:
For a given AND gate $(\alpha, \beta, \gamma)$, assume that parties only have secret shares of $\lambda_{\alpha}$, $\lambda_\beta$, $\lambda_\gamma$ and $\lambda_\alpha \land \lambda_\beta$. This can be (reactively) preprocessed, it's independent of the input values. 

Recall that $\hat{z}_{00} = (\lambda_\alpha \land \lambda_\beta)\oplus \lambda_\gamma$ so $P_A$ and $P_B$ can obtain secret shares of this using preprocessed material. Expanding this out, $P_A$ and $P_B$ now hold $[r]_A$ and $[s]_B$ such that $r\oplus s = \hat{z}_{00}$. In more detail, $P_A$ holds $(r, M[r], K[s])$ and $P_B$ holds $(s, M[s], K[r])$ where $M[r]\oplus K[r] = r\Delta_B$ and $M[s]\oplus K[s] = s\Delta_A$. This can be used to get a secret share of the label $L_{\gamma, \hat{z}_{00}} = L_{\gamma, 0} \oplus \hat{z}_{00}\Delta_A$ (we are setting the FreeXOR $\Delta = \Delta_A$ which is $A$'s global MAC for BDOZ shares). This is a good idea because parties already hold a share of $\hat{z}_{00}\Delta_A = r\Delta_A \oplus s\Delta_A = r\Delta_A \oplus K[s] \oplus M[s]$ since $A$ can easily compute $r\Delta_A\oplus K[s]$ and $B$ holds $M[s]$. [should be able to extract more value here... double GC idea?] Actually, the above calculation shows that they can add any $\lambda\Delta_A$ to the shared label, where $\lambda$ is authenticated secret shared between the two.  

This shows that for the $(0,0)$ entry, having authenticated shares of random masks (and their AND) is enough to share the output mask as well as its corresponding label (needed for future decryptions). [again can be used for something later...] In fact, the same is true for all entries $(i,j)$ of the table. This is because (upon expanding):
- $\hat{z}_{00} = (\lambda_\alpha \land \lambda_\beta)\oplus \lambda_\gamma$
- $\hat{z}_{01} = \hat{z}_{00} \oplus \lambda_\alpha$
- $\hat{z}_{10} = \hat{z}_{00} \oplus \lambda_\beta$
- $\hat{z}_{11} = \hat{z}_{00} \oplus \lambda_\alpha \oplus \lambda_\beta \oplus 1$
With this, $P_A$ and $P_B$ can modify their shares of $\hat{z}_{ij}$ and the label $L_{\gamma, \hat{z_{ij}}}$ by adding for example $\lambda_a\Delta_A$ to $L_{\gamma, \hat{z}_{00}}$ to get $L_{\gamma, \hat{z}_{01}}$ as seen in the previous calculation. 

One more thing encrypted in the garbled table is the MAC on $P_A$'s share which can be opened and checked by $P_B$ after decrypting to ensure that $P_A$ didn't send an incorrect share. 
### Full Protocol
#### Assuming we have $\mathcal{F}_{pre}$
We start with the preprocessing material:
- Global MAC keys $\Delta_A, \Delta_B$ used for authenticated bits. 
- Authenticated shares of secret shared $\lambda = r\oplus s$ (cf. [[#Secret-sharing]]) for each input wire and AND gate output wires.

Using the technique mentioned in [[#Efficient Realization combining BDOZ shares and FreeXOR]], in the function-dependent (but input-independent) phase, $P_A$ and $P_B$ can go through the gates and construct secret shared garbled tables for all the gates. For XOR gates, they can locally do it and for AND gates, they can invoke $\mathcal{F}_{pre}$ by inputting authenticated shares of $\lambda_\alpha, \lambda_\beta$ and obtain an authenticated share of $\lambda_\alpha \land \lambda_\beta$ and then construct the garbled table locally. 

For completeness, we look at the detailed example of one AND gate for positions $(0,0)$ and $(0,1)$. These are all the steps from beginning to end:

1. For input wires $\alpha$, $\beta$ and output wire of AND gate $\gamma$ the parties have authenticated secret shares of random masks $\lambda_\alpha, \lambda_\beta, \lambda_\gamma$. $P_A$ has also picked $L_{\alpha, 0}, L_{\beta,0}, L_{\gamma, 0}$  uniformly from $\{0,1\}^k$.
2. In the function-dependent phase, they send their authenticated secret shares of $\lambda_\alpha, \lambda_\beta$ to $\mathcal{F}_{pre}$ to obtain authenticated secret shares of $r \oplus s = \lambda_\alpha \land \lambda_\beta$ along with MACs and keys w.r.t global MAC keys. 
3. Using this, the prepare shares of $\hat{z}_{00}$ and $\hat{z}_{01}$ and garbled label shares as follows: [can make this more compact using some notation for authenticated shared] $P_A$ locally computes $(r_{00}, M[r_{00}], K[s_{00}]) = (r, M[r], K[s])\oplus(r_\gamma, M[r_\gamma], K[s_\gamma])$ and $P_B$ locally computes $(s_{00}, M[s_{00}], K[r_{00}]) = (s, M[s], K[r])\oplus(s_\gamma, M[s_\gamma], K[r_\gamma])$ so that $r_{00} \oplus s_{00} = (r\oplus s) \oplus (r_\gamma \oplus s_\gamma) = (\lambda_\alpha \oplus \lambda_\beta) \oplus \lambda_\gamma = \hat{z}_{00}$ and they hold an authenticated secret share of $\hat{z}_{00}$ as required. Similar local computation gives them the other $r_{ij}, s_{ij}$ as well. An invariant is that at every step, they have MACs on their shares which can be revealed to the other person to convince them that they haven't tampered with the shares. 
4. $P_A$ computes $L_{\alpha,1}, L_{\beta, 1}$ by XORing with $\Delta_A$ and sends the following garbling to $P_B$:
	1. $G_{\gamma, 00} = H(L_{\alpha, 0}, L_{\beta, 0}, \gamma, 00) \oplus (r_{\gamma, 00}, M[r_{\gamma, 00}], L_{\gamma, \hat{z}_{00}}^A)$ where the share of the label $L_{\gamma, \hat{z}_{00}}^A$ is created using the trick in [[#Efficient Realization combining BDOZ shares and FreeXOR]].
	2. Similarly, it computes $G_{\gamma, 01}$ and the others as well with the respective shares and then sends it over to $P_B$. 
5. INPUTS (surprisingly no OT?): First for the input wires $\alpha, \beta$ assuming that $\alpha$ is $P_A$'s input while $\beta$ is $P_B$'s input. 
	1. $P_A$ sends and authenticates its shares of $\lambda_\alpha$ to $P_B$ who checks it and sends the other share of the masked input to $P_A$ who finally sends $L_{\alpha, x_{\alpha}\oplus \lambda_\alpha}$. $P_A$ authenticates this because this is the masked value that $P_B$ uses during evaluation. 
	2. $P_B$ sends and authenticates its shares of $\lambda_\beta$ to $P_A$ who verifies combines the shares to send $L_{\beta, x_{\beta}\oplus \lambda_\beta}$ to $P_B$. [how does B know it gets the right wire label? maybe it just makes the random generated wire different...]
6. Circuit evaluation: $P_B$ receives the masked outputs and corresponding labels. It also has the encrypted garbled circuit which can decrypted with $L_{\alpha, \hat{x}_\alpha}$ and $L_{\beta, \hat{x}_{\beta}}$ and by pointing at it using $\hat{x}_\alpha, \hat{x}_\beta$ which $P_B$ knows. Since this was an AND gate, $P_B$ already has a share of the masked output $\lambda_\gamma$ and decrypting the table gives $P_B$ the other share (along with its MAC for authentication). It also gives $P_B$ one share of the label $L_{\gamma, \hat{z}_{\gamma}}$, while it already has the other share from the function-dependent preprocessing. This finishes the inductive step to get output masked values and corresponding labels. 
7. For output wires, $P_A$ sends its share of the *mask* who authenticates it then unmasks the output using its share of the *mask*.  [there is also a way to make this work for reveal to both]
#### Instantiating $\mathcal{F}_{pre}$

TODO

## Rigorous security proof

---

[HSS](https://eprint.iacr.org/2017/214.pdf) paper.