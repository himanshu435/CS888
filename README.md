# Secure Inner Product with Additive Secret Sharing

- You can run the code as:
  - `docker-compose build`
  - `docker-compose up`

---

## Additive Secret Sharing

We work over the ring $\mathbb{Z}_{2^{32}}$.

Any secret $x \in \mathbb{Z}_{2^{32}}$ is split between two parties $P_0$ and $P_1$ as:

$$
x = x_0 + x_1 \pmod{2^{32}}
$$

- $x_0 \gets_R \mathbb{Z}_{2^{32}}$ chosen uniformly at random.
- $x_1 = x - x_0 \pmod{2^{32}}$

Thus:

- $P_0$ learns only $x_0$, which is uniformly random.
- $P_1$ learns only $x_1$, which is uniformly random.

Neither share alone reveals information about $x$, so the scheme is *perfectly secure*: for any $x$, the distribution of each share is independent of the secret.


---

Secure Multiplication via Beaver Triples

Given secret-shared values $[x] = (x_0, x_1)$ and $[y] = (y_0, y_1)$, we want to compute $[z]$ such that:

𝑧
=
𝑥
⋅
𝑦
,
[
𝑧
]
=
(
𝑧
0
,
𝑧
1
)
z=x⋅y,[z]=(z
0
	​

,z
1
	​

)
Preprocessed Triple

A Beaver triple is a preprocessed sharing:

[
𝑎
]
,
[
𝑏
]
,
[
𝑐
]
with
𝑐
=
𝑎
⋅
𝑏
[a],[b],[c]withc=a⋅b

where each value is secret-shared between the parties.

Protocol

Each party locally computes:

𝑒
𝑖
=
𝑥
𝑖
−
𝑎
𝑖
,
𝑓
𝑖
=
𝑦
𝑖
−
𝑏
𝑖
e
i
	​

=x
i
	​

−a
i
	​

,f
i
	​

=y
i
	​

−b
i
	​


and exchanges $(e_i, f_i)$.

After exchange, both reconstruct:

𝑒
=
𝑒
0
+
𝑒
1
,
𝑓
=
𝑓
0
+
𝑓
1
e=e
0
	​

+e
1
	​

,f=f
0
	​

+f
1
	​


Note: $e = x - a$, $f = y - b$, but neither $x$ nor $y$ is revealed.

Each party computes its share of $z$:

𝑧
𝑖
=
𝑐
𝑖
+
𝑒
⋅
𝑏
𝑖
+
𝑓
⋅
𝑎
𝑖
+
𝛿
𝑖
⋅
(
𝑒
⋅
𝑓
)
,
z
i
	​

=c
i
	​

+e⋅b
i
	​

+f⋅a
i
	​

+δ
i
	​

⋅(e⋅f),

where $\delta_0 = 1, \delta_1 = 0$.

Summing yields:

𝑧
=
𝑧
0
+
𝑧
1
=
𝑎
𝑏
+
𝑒
𝑏
+
𝑓
𝑎
+
𝑒
𝑓
=
(
𝑎
+
𝑒
)
(
𝑏
+
𝑓
)
=
𝑥
𝑦
z=z
0
	​

+z
1
	​

=ab+eb+fa+ef=(a+e)(b+f)=xy

Thus multiplication is securely computed in one round.

---

## Secure Inner Product

Given two secret-shared vectors:

$$
[u] = ([u_1], \ldots, [u_n]), \quad [v] = ([v_1], \ldots, [v_n])
$$

we want to compute:

$$
d = \langle u, v \rangle = \sum_{i=1}^n u_i v_i
$$

### Naïve Approach

Compute each product \([u_i v_i]\) via Beaver triples, then sum locally.  
This requires \(n\) secure multiplications and thus \(n\) rounds.

### Batched Approach

Mask vectors at once:

$$
e = u - a, \quad f = v - b
$$

for vector triples \((a, b, c)\) with \(c = \langle a, b \rangle\).  

Then:

$$
\langle u, v \rangle = \langle a, b \rangle + \langle e, b \rangle + \langle f, a \rangle + \langle e, f \rangle
$$

This yields the inner product in a **single round**.

---

## Secure Update of Dot Products

Suppose we already hold \([d] = [\langle u, v \rangle]\).  
If \(v\) is updated to \(v' = v + \Delta v\), then:

$$
\langle u, v' \rangle = \langle u, v \rangle + \langle u, \Delta v \rangle
$$

Thus:

1. Compute correction term \(\Delta = \langle u, \Delta v \rangle\) securely.  
2. Update \([d'] = [d] + [\Delta]\) locally.  

If only a few coordinates change, the cost is proportional to the number of updates.

---

## Communication Rounds

- One multiplication requires one round (exchange of \(e,f\)).  
- Naïve inner product: \(n\) multiplications \(\Rightarrow n\) rounds.  
- Batched inner product: one round for the entire dot product.  
- Updates: only require as many multiplications as changed coordinates.  

---

## Efficiency and Security

- **Perfect privacy**: each party’s view is independent of the secrets.  
- **Preprocessing**: Beaver triples can be generated offline.  
- **Online efficiency**: one round per multiplication (or per dot product if batched).  
- **Incremental updates**: only recompute correction terms.  

---

## Acknowledgements

- I want to thank my friends Paritosh and Riya with whom I discussed the assignment. I also discussed the assignment with Udbhav, who is not enrolled in the course but has knowledge about this course.  
- I also want to acknowledge that I have used ChatGPT for some code refactoring and for corrections in the documentation.
