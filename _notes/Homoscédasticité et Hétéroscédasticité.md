---
title: Homoscédasticité et Hétéroscédasticité
tags: Probabilités
etat: hiver
---

On appelle **homscédasticité** l'égalité de la variance d'une variable quelque soit l'échantillon qui y est puisé. Lorsque la variance diffère selon les échantillons, on parle alors d'**hétéroscédasticité**.

## Tester l'homoscédasticité

L'homoscédasticité peut être vérifiée avec le test de Levene (non sensible à la non-normalité - à privilégier), de Barlett (à privilégier si la distribution est normale) ou de Fisher (moins robuste en l'absence de normalité - déconseillé). 

Ces tests ont en commun d'examiner l'hypothèse nulle de l'égalité des variances :

$$H_0 : \sigma^2_1 = \sigma^2_2 = ... = \sigma^2_k$$

contre l'hypothèse alternative :

$$H_a : \sigma^2_1 \neq \sigma^2_2$$ pour au moins une paire (i, j).

## Application

[[Scipy]] propose des méthodes permettant d'utiliser ces tests :

- Test de Levene : [scipy.stats.levene](https://docs.scipy.org/doc/scipy/reference/generated/scipy.stats.levene.html)
- Test de Barlett : [scipy.stats.bartlett](https://docs.scipy.org/doc/scipy/reference/generated/scipy.stats.bartlett.html)