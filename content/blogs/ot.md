---
title: Introduction to Oblivious Transfer
---
[cool!] look at non-interactive secure computation (IKOPS paper was one of seminal), because silent OT has ramifications for this.

Almost all (others use SHE) MPC protocols use OT (and therefore OT extension) in the preprocessing phase. This is how OT extension has progressed. 

Started with [IKNP](https://www.iacr.org/archive/crypto2003/27290145/27290145.pdf) that found a clever efficient way to implement Beaver's GC idea of doing OT extension. This is the "tilt your head" to take matrix transpose idea. 

Then, this was upgraded to malicious security in [KOS](https://eprint.iacr.org/2015/546.pdf) using similar techniques. 

Finally, work by [BCGI](https://eprint.iacr.org/2019/1159.pdf) from 2018-2019 made it super efficient by assuming LPN. They call it Silent OT and it belongs to a family of work of silent VOLE, PCG, etc. Bar Ilan latest MPC workshop covers a lot of this. 

Finally, this work [Roy22](https://eprint.iacr.org/2022/192) discusses tradeoffs in computation vs communication for OT extension and improves IKNP without assuming LPN. 

There is also [Ferret](https://eprint.iacr.org/2020/924.pdf) for correlated OTs which is ultrafast and builds on Silent OT. 

MPZ implements KOS and Ferret. In this post, I'll explore the entire history of OT extension. 

We start by examining **KOS Active OT extension** and **BCG+ Silent OT**. 

## KOS Active OT Extension

The idea is to use semi-honest IKNP trick with a consistency check and then sacrifice some extended OTs. It is similar to previous active OT works but this is the most efficient and is only 6% extra cost for 1 million OTs. Also, these OT extensions are on strings of length at least $k$. [does this mean OT message length or number of base OTs?]

It turns out that this consistency check only requires a constant number of hash computations and on fixed input length, does not grow with the size of extended OTs. 

They generate random OTs and prove security in the random oracle model. Note that random OTs are useful because this is what we want for MPC with preprocessing. 

The paper has some interesting notation for identifying $\mathbb{F}_2^k$ and $\mathbb{F}_{2^k}$. 

Start with semi-honest IKNP as this builds on it extensively
###  IKNP semi-honest OT extension
This [project](https://nishkum.github.io/files/OT_extension.pdf) from 2020 seems like a great resource for IKNP. It also mentions that Ferret is now better... 

Some facts about OT:
1. $k$ instances of Random OT for messages of length $m$ can be viewed as a protocol for the following: Sender gets a random $m$ bit string $\Delta_i$ for all $i \in [k]$, Receiver gets a random bit $b_i$ for all $i \in [k]$ and they also receive secret shares of all $b_i\Delta_i$ for $i \in [k]$. 
2. $k$ instances of Correlated Random OT (CROT) for messages of length $m$ is the exact same thing except the Sender only gets one $m$ bit string $\Delta$, Receiver still gets $k$ random bits $b_i$ and they receive shares of $b_i \Delta$. 
3. Random OT $\Rightarrow$ OT with $O(m)$ communication using an information theoretic mask. 
4. Correlated Random OT $\Rightarrow$ random OT by using Random oracle (correlation robust hash function) by hashing shares to break the correlation. 
5. Length $k$ OT where $k$ is at least security parameter implies length $n = poly(k)$ ROT by encrypting keys for a PRG and then locally expanding.  

Our goal is to now show one can use $k$ instances of length $k$ OT to do $m = poly(k)$ instances of length $m$ OT. First, it suffices to do $m$ instances of length $k$ OT by point 5 and 3. In fact, it even suffices to do $m$ instances of length $k$ Correlated Random OT by point 4 and point 3 if we are willing to use random oracle/ correlation robust hash functions. Lastly, it suffices to show that this can be done from $k$ instances of length $m$ OT because the original length $k$ OTs can easily be expanded to work with length $m$ OTs by point 5. 

We are left to show that one can use few instances of OT for long messages to get many instances of CROT for short messages. We will prove something even stronger (well not really if we're assuming RO but it sounds stronger): few instances of CROT for long messages is the equivalent to many instances of CROT for short messages. This is the IKNP OT extension idea and it's nothing more than a shift in perspective: 

First we look at few instances of CROT for long messages. The sender gets a long message $\vec{\sigma}$ (viewed as a long row) and the receiver gets few random bits $b_1, \ldots, b_k$ (stacked into a short column $\vec{b}$). They also receive few long shares of $b_i \vec{\sigma}$ for $i \in [k]$ (remember, this is what CROT gives you by point 2). They view these shares as long rows and stack them into a short and fat matrix as below:

$$
\begin{pmatrix}
b_1 \\ b_2 \\ b_3
\end{pmatrix}, 
(\quad\quad\longleftarrow\sigma \longrightarrow\quad\quad), 
\begin{pmatrix}
\quad\quad\longleftarrow [b_1\sigma] \longrightarrow\quad\quad \\ 
\quad\quad\longleftarrow [b_2\sigma] \longrightarrow\quad\quad \\
\quad\quad\longleftarrow [b_3\sigma] \longrightarrow\quad\quad
\end{pmatrix}
$$
Now, instead look at a many instances of CROT for short messages. We will also suggestively switch the way we represent these now. The sender gets a short message $\vec{b}$ (viewed as a short *column*), the receiver gets many random bits $\sigma_1, \ldots, \sigma_m$ (stacked into a long *row* $\vec{\sigma}$) and they many short shares receive shares of $\vec{b}\sigma_i$ for $i \in [m]$. They now view these shares as short *columns*, which allows them to again stack them into a short and fat matrix below. (If we used the same convention as the previous case here then we would instead get a tall and skinny matrix i.e. the transpose of this one).
$$
\begin{pmatrix}
\uparrow \\ b \\ \downarrow
\end{pmatrix}, 
(\sigma_1, \sigma_2, \sigma_3, \sigma_4, \sigma_5), 
\begin{pmatrix}
\uparrow  & \uparrow & \uparrow & \uparrow & \uparrow\\ 
[b\sigma_1] & [b\sigma_2] & [b\sigma_3] & [b\sigma_4] & [b\sigma_5]\\
\downarrow & \downarrow & \downarrow & \downarrow & \downarrow
\end{pmatrix}
$$
This key insight is that these matrices are exactly the same as the $(i,j)$-th entry holds $b_i\sigma_j$. Therefore, the Sender and Receiver that wish to execute a protocol to obtain many short CROT instances can do the following: switch their roles and run the protocol for a few instances of CROT for long messages. S (acting as the receiver) gets a short $k$ bit string $\vec{b}$ and R (acting as the sender) gets a long $m$ bit string $\vec{\sigma}$. They also gets few long shares of $b_i\sigma$. 

Next, they tilt their head and view these as many short shares of $b\sigma_i$. As required, S now has a short correlation $\vec{b}$, R has many choice bits $\sigma_i$ for $i \in [m]$ and they have shares of $b \sigma_i$ as required for many instances of CROT for short messages. 
### SoftSpokenOT
Some issues were fixed by SoftSpokenOT [Roy22] which is a more recent work, uses VOLE ideas to do better than IKNP communication asymptotically (with higher computation). Reading this full paper as well might be a good idea. 
## Silent OT
Also look at its application to NISC, initiated in [IKOPS11](https://web.cs.ucla.edu/~rafail/PUBLIC/118.pdf) by Rafi using parallel 2 round OT (which is what Silent OT claims to achieve) and it's what Vivek wants (but is okay with semi-honest security). This was implemented in [AMPR14](https://eprint.iacr.org/2015/282.pdf)  but uses an expensive cut-and-choose technique. This more recent (2019) [work](https://eprint.iacr.org/2018/940.pdf) improves to reusable NISC, has Rafi and and Melissa Chase. [I conjecture that it should be more efficient now...]. This is an even more recent reusable NISC work [MrNISC](https://eprint.iacr.org/2020/221.pdf) that talks about witness encryption and might be similar to Vivek's idea. This latest [work](https://eprint.iacr.org/2023/072.pdf) does it specifically for inner product. 







