---
author: bradben
description: Learn about the Jordan-Wigner Representation, which maps Hamiltonian operators to unitary matrices that can more easily implemented on a quantum computer.
ms.author: brbenefield
ms.date: 06/21/2023
ms.service: azure-quantum
ms.subservice: qsharp-guide
ms.topic: conceptual
no-loc: ['Q#', '$$v']
title: Jordan-Wigner Representation
uid: microsoft.quantum.libraries.overview-chemistry.concepts.jordanwigner
---

# Jordan-Wigner representation

While [second quantized Hamiltonians](xref:microsoft.quantum.libraries.overview-chemistry.concepts.secondquantization) are conveniently represented in terms of $a^\dagger$ (creation) and $a$ (annihilation), these operations aren't fundamental operations in quantum computers.
As a result, if you wish to implement them on a quantum computer, you need to map the operators to unitary matrices that can be implemented on a quantum computer.
The Jordan–Wigner representation gives one such map.
However, others, such as the Bravyi–Kitaev representation, also exist and have their own relative advantages and disadvantages.
The main advantage of the Jordan-Wigner representation is its simplicity.

The Jordan-Wigner representation is straightforward to derive.
Recall that a state $\ket{0}_j$ implies that spin-orbital $j$ is empty and $\ket{1}_j$ implies that it is occupied.
This means that qubits can naturally store the occupation of a given spin-orbital.
You then have that $a^\dagger_j \ket{0}_j = \ket{1}_j$ and $a^\dagger_j \ket{1}_j = 0$.
It is easy to verify that

\begin{align}
a^\dagger_j &= \begin{bmatrix}0 & 0 \\\\ 1 &0 \end{bmatrix}=\frac{X_j - iY_j}{2}, \nonumber\\\\
a_j &= \begin{bmatrix}0 & 1 \\\\ 0 &0 \end{bmatrix}=\frac{X_j + iY_j}{2},
\end{align}

where $X_j$ and $Y_j$ are the Pauli-$X$ and -$Y$ operators acting on qubit $j$.

>[!NOTE]
> In Q# the $\ket{0}$ state represents the +1 eigenstate of the $Z$ operator. In some areas of physics $\ket{0}$ represents the low-energy ground state and thus the -1 eigenstate of the $Z$ operator. Therefore, some formulas might differ from popular literature.

The Azure Quantum chemistry library uses $\ket{0}$ to represent an unoccupied spin-orbital.
This shows that for a single spin-orbital, it is easy to represent creation and annihilation operators in terms of unitary matrices that quantum computers understand.
 Note that while $X$ and $Y$ are unitary, $a^\dagger$ and $a$ are not.
 You'll see later that this doesn't pose a challenge for simulation.

One problem that remains is that while the previous construction works for a single spin-orbital, it fails for systems with two or more spin orbitals.
Since Fermions are antisymmetric, you know that $a^\dagger_j a^\dagger_k = - a^\dagger_k a^\dagger_j$ for any $j$ and $k$.
However,

$$
\left(\frac{X_j - iY_j}{2}\right)\left(\frac{X_k - iY_k}{2}\right) = -\left(\frac{X_k - iY_k}{2}\right) \left(\frac{X_j - iY_j}{2}\right).
$$

In other words, the two creation operators don't anti-commute as required.
This can be remedied, though, in a straightforward, if inelegant fashion.
The fix is to note that Pauli operators naturally anti-commute.
In particular, $XZ = -ZX$ and $YZ=-ZY$.
Thus, by interspersing $Z$ operators into the construction of the operator, you can emulate the correct anti-commutation.
 The full construction is as follows:

\begin{align}
a^\dagger_1 &= \left(\frac{X-iY}{2}\right)\otimes 1 \otimes 1 \otimes 1 \otimes \cdots \otimes 1,\\\\
a^\dagger_2 &= Z\otimes\left(\frac{X-iY}{2}\right)\otimes 1\otimes 1 \otimes \cdots \otimes 1,\\\\
a^\dagger_3 &= Z\otimes Z\otimes \left(\frac{X-iY}{2}\right)\otimes 1 \otimes \cdots \otimes 1,\\\\
&\vdots\\\\
a^\dagger_N &= Z\otimes Z\otimes Z\otimes Z \otimes \cdots \otimes Z\otimes \left(\frac{X-iY}{2}\right). \label{eq:JW}
\end{align}

It is also convenient to express the number operators, $n_j$, in terms of Pauli operators.
Thankfully, the strings of $Z$ operators (known as Jordan-Wigner strings) cancel after one makes this substitution.
After carrying this out (and recalling that $X_jY_j=iZ_j$), you have

$$
n_j = a^\dagger_j a_j = \frac{(1-Z_j)}{2}.
$$

## Constructing Hamiltonians in Jordan-Wigner representation

Once you have invoked the Jordan-Wigner representation, translating the Hamiltonian to a sum of Pauli operators is straightforward.
You simply have to replace each of the $a^\dagger$ and $a$ operators in the Fermionic Hamiltonian with the strings of Pauli-operators shown earlier.
When you perform this substitution, only five classes of terms are within the Hamiltonian.
These five classes correspond to the different ways you can pick the $p,q$ and $p,q,r,s$ in the one-body and the two-body terms in the Hamiltonian.
These five classes, for the case where $p>q>r>s$ and real-valued orbitals, are

\begin{align}
h_{pp}a_p^\dagger a_p &= \sum_p \frac{h_{pp}}{2}(1 - Z_p)\\\\
h_{pq}(a_p^\dagger a_q + a^\dagger_q a_p) &= \frac{h_{pq}}{2}\left(\prod_{j=q+1}^{p-1} Z_j \right)\left( X_pX_q + Y_pY_q\right)\\\\
h_{pqqp} n_p n_q &=  \frac{h_{pqqp}}{4}\left(1-Z_p - Z_q +Z_pZ_q \right)\\\\
H_{pqqr} &= \frac{h_{pqqr}}{2}\left(\prod_{j=r+1}^{p-1} Z_j \right)\left( X_pX_r + Y_pY_r\right)\left(\frac{1-Z_q}{2}\right)\\\\
H_{pqrs} &= \frac{h_{pqrs}}{8}\prod_{j=s+1}^{r-1} Z_j\prod_{k=q+1}^{p-1} Z_k \Big(XXXX - XXYY+XYXY\nonumber\\\\
&\qquad\qquad\qquad\qquad\qquad+YXXY+YXYX-YYXX\nonumber\\\\
&\qquad\qquad\qquad\qquad\qquad+XYYX+YYYY\Big)
\end{align}

While generating such Hamiltonians by hand only requires applying these replacement rules, doing so would be infeasible for large molecules consisting of millions of Hamiltonian terms.
As an alternative, you can automatically construct the `JordanWignerEncoding` given a `FermionHamiltonian` representation of the Hamiltonian.

```csharp
// Make sure to load these at the top of your file or namespace.
using Microsoft.Quantum.Chemistry.Fermion;
using Microsoft.Quantum.Chemistry.Paulis;
```

```csharp
    // Create an example `FermionHamiltonian` instance.
    var fermionHamiltonian = new FermionHamiltonian();

    // Convert this Fermion Hamiltonian to a Jordan-Wigner representation.
    var jordanWignerEncoding = fermionHamiltonian.ToPauliHamiltonian(QubitEncoding.JordanWigner);
```

Once the Hamiltonians are constructed in this form, you can use a host of quantum simulation algorithms to compile the dynamics generated by $e^{-iHt}$ into a sequence of elementary gates (within some user-definable error tolerance).
For more information on the two most popular methods for quantum simulation, qubitization, and Trotter–Suzuki formulas, see [Simulating Hamiltonian dynamics](xref:microsoft.quantum.libraries.overview-chemistry.concepts.simulationalgorithms), which also provides examples of using the Hamiltonian simulation library to implement both methods.
