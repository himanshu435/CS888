# Secure Inner Product with Additive Secret Sharing

- You can run the code as:
  - `docker-compose build`
  - `docker-compose up`

---

## Additive Secret Sharing

We work over the ring \(\mathbb{Z}_{2^{32}}\).  
Any secret \(x \in \mathbb{Z}_{2^{32}}\) is split between two parties \(P_0\) and \(P_1\) as:

$$
x = x_0 + x_1 \pmod{2^{32}}
$$

- \(x_0 \gets_R \mathbb{Z}_{2^{32}}\) chosen uniformly at random.  
- \(x_1 = x - x_0 \pmod{2^{32}}\).  

Thus:

- \(P_0\) learns only \(x_0\), which is uniformly random.  
- \(P_1\) learns only \(x_1\), which is uniformly random.  

Neither share alone reveals information about \(x\), so the scheme is *perfectly secure*: for any \(x\), the distribution of each share is independent of the secret.

---

## Secure Multiplication via Beaver Triples

Given secret-shared values \([x] = (x_0, x_1)\) and \([y] = (y_0, y_1)\), we want to compute \([z]\) such that:

$$
z = x \cdot y, \qquad [z] = (z_0, z_1)
$$

### Preprocessed Triple

A *Beaver triple* is a preprocessed sharing:

$$
[a], [b], [c] \quad \text{with} \quad c = a \cdot b
$$

where each value is secret-shared between the parties.

### Protocol

1. Each party locally computes:

   $$
   e_i = x_i - a_i, \qquad f_i = y_i - b_i
   $$

   and exchanges \(e_i, f_i\).

2. After exchange, both reconstruct:

   $$
   e = e_0 + e_1, \qquad f = f_0 + f_1
   $$

   Note that \(e = x - a, f = y - b\), but neither \(x\) nor \(y\) is revealed.

3. Each party computes its share of \(z\):

   $$
   z_i = c_i + e \cdot b_i + f \cdot a_i + \delta_i \cdot (e \cdot f),
   $$

   where \(\delta_0 = 1, \delta_1 = 0\).

Summing yields:

$$
z = z_0 + z_1 = ab + e b + f a + e f = (a+e)(b+f) = xy
$$

Thus multiplication is securely computed in **one round**.

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
