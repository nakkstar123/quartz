Pseudorandom Correlation Generators
Used in 2-round OT extension and more generally all silent OT/(V)OLE protocols. 
High level: interaction to share a short seed between Alice and Bob, silent expansion to get a large correlation for MPC. Very low communication, higher computation. 
Reference: [PCG Part 1](https://www.youtube.com/watch?v=A2jWB6mlUPE&t=2090s)

Despite IKNP, OT is noisy. Even for bit OT, need 100 bits of communication per OT [how?]. 

**Part 1:**
Low-end constructions from symmetric cryptography
**Part 2:**
Getting to OT and VOLE stuff 

Different approaches for 2PC (and comparisons for semi-honest parties)
![[Screenshot 2024-10-08 at 11.52.03 AM.png]]
GCs are low constant round (independent of C and I) but have high communication, up to 100 bits per AND (or any non-linear) gate and they don't generalize well to arithmetic circuits, only binary circuits. 

LSS like GMW can get much better communication (with preprocessing) but it has round complexity in depth of the circuit which can be too much. But it naturally generalizes to Arithmetic circuits. 

FHE has low asymptotic communication: linear in size of *smaller* input but very high communication costs and even the communication can be concretely high for small inputs. 

HSS (relatively new, dual of FSS) can have lower computation (esp for restricted function classes) but still heavy and good communication, just that it is now the sum of both parties input sizes. 

![[Screenshot 2024-10-08 at 11.56.34 AM.png]]
These are the HSS tools we currently have with different levels of assumptions, the most recent an interesting work seems to be in Lapland which will be the focus of this talk. 

## Comparison with honest-majority 3PC
[ALFNO16] which I can't find but there seem to be more recent works as well. Apparently this achieves communication of 1 bit per AND (for binary circuits) and even less computation. The question is whether we can do the same for 2PC. 

Issue: HSS/FHE has heavy computation and Yao/GMW + OT Extension has heavy communication (worse by 2 orders of magnitude!)

This is already *solved* in the setting where a trusted dealer gives correlated randomness :)
This gives us an online phase (for both binary and arithmetic circuits) gives us something as efficient (small multiplicative computation constant over insecure evaluation) and fast in practice! 

The main problem -- interactive preprocessing is a huge bottleneck, bigger by orders of magnitude! Goal of PCG is to minimize this overhead. 

You don't want the seed to give up anything extra to Alice/Bob that the target correlation should not give. 

**MPC with silent preprocessing**
Here: we meet the challenge if we look at online computation and total communication... If we have fast expansion, we fully meet the challenge. So that is the remaining issue now. 

*To protect against malicious parties, we only need to protect Phase 1 (and also generate some additional correlations for check in the online phase) but this phase is small so in fact the malicious overhead is vanishing*.

Philosophically, this 3 phase separation has a bunch of benefits:
1. It's like exchanging business cards. 
2. You don't need to do the future interaction, and the correlated randomness is cheap to store as well cuz it's compressed. 
3. It doesn't reveal anything about the function you would want to compute (WRK has a circuit dependent offline phase, does this improve on that?)

**Concrete PCG setup phase:** Covered in Peter's talks... 

*One distinction from Laconic SFE is that they don't have an info-theoretic online phase.*

> [!WARNING]
> The PCG security definition is NOT "in the ideal world, just give long correlated randomness to both parties". 

This is impossible to implement even for a simple correlation like equality. Why? 
In the real world: the adversary gets a seed and then expands it. In other words, it has a short explanation for the honest party's output. In the ideal world, a simulator cannot get this short seed. 

There is a natural tweak which suffices:
In the ideal world, instead of the TTP doing:
1. Sample $(r_0, r_1)$ from $(R_0,R_1)$ 
2. Give $r_0$ to Alice and $r_1$ to Bob, 
We consider a slightly corrupted ideal world. Here, 
1. The TTP *accepts* $r_0$ sampled from $R_0$ from Alice. 
2. TTP samples $r_1$from $R_1|R_0 = r_0$ and gives it to Bob. 

The simulator now needs to be able to generate the same view as the real world. 

[need to see a protocol that realizes this security -- See Peter's talk]

This is a relaxed definition because: if one securely distributes keys and then locally expands, this definition is met. But even if the keys aren't securely distributed and then some expansion is done, there's a way to meet this definition. 

This tweak suffices for correlations such as shares of random values (Beaver triples) because the adversary just gets one random share $r_0$ which does not let it control the other share or the actual value. 

## Useful target correlations

In the 3+ party case:
- N x linear additive shares of 0
- N x shares of shamir poly of a random secret
In the 2+ party case: 
- N x ROT correlations 
- N x R-OLE / 1 R-VOLE correlation
![[Screenshot 2024-10-08 at 12.50.57 PM.png]]
- authenticated multiplication triples (2PC of malicious arithmetic circuits)
- random shifted secret-shared TT (2PC of unstructured functions)

## Feasibility
![[Screenshot 2024-10-08 at 12.54.48 PM.png]]
And only the bottom two are practically efficient. LPN would be most interesting for now as it is not too big of an assumption, practically efficient and gets what we need for MPC. Also, there are lines of work after BCG+ that make it even more efficient. 

**For OT specifically, at a point where not only communication is better than OT extension but even computation is better** (under a slight variant of LPN which is plausible)

Remark: HSS gives us PCGs for additive correlation but it's not practically feasibly [skipped]

## PCG from Minicrypt
[skipped] last 10 mins of lecture, really complicated ideas but didn't talk about OT/VOLE

## PCG from Lapland
next talk

3rd talk discusses silent OT in great depth, will probably be the most useful!
