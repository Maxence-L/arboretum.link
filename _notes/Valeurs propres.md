---
title: Valeurs propres
tags: Maths
etat: hiver
---

**Définition :** Une transformation $\mathrm{t}: \mathrm{V} \rightarrow \mathrm{V}$ possède une *valeur propre* scalaire $\lambda$ s'il existe un *vecteur propre* non-nul $\vec{\zeta} \in \mathrm{V}$ telle que $\mathrm{t}(\vec{\zeta})=\lambda \cdot \vec{\zeta}$

**Exemple** : La projection

$$\left(\begin{array}{l}
x \\
y \\
z
\end{array}\right) \stackrel{\pi}{\longmapsto}\left(\begin{array}{l}
x \\
y \\
0
\end{array}\right) \quad x, y, z \in \mathbb{C}$$

possède une valeur propre égale à 1 associée au vecteur propre

$$\left(\begin{array}{l}
x \\
y \\
0
\end{array}\right)$$

où $x$ et $x$ sont des scalaires non-nuls. A l'inverse, $2$ n'est pas une valeur propre de la projection. Cela reviendrait en effet à doubler le vecteur de telle manière que $x=2x$, $y=2y$, et $0=2z$ - ce qui n'est pas possible pour un vecteur propre différent de 0 ($\vec{\zeta} = \vec{0}$).

---

**Définition :** Une matrice carrée $T$ possède une *valeur propre* scalaire $\lambda$ associée au *vecteur propre* non-nul $\vec{\zeta}$, si $T\vec{\zeta}=\lambda \cdot \vec{\zeta}$

