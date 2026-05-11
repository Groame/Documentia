# Configuration Matrices - Combinatorics Summary

## Problem

Count the number of valid configuration vectors $[d, s, r_1, r_2, \ldots, r_k]$ where:

| Column | Description | Constraints |
|--------|-------------|-------------|
| $d$ | Number of DUs | $1 \leq d \leq N_{DU}$ |
| $s$ | Number of switches | $0 \leq s \leq R$ |
| $r_1 \ldots r_k$ | Radios of type $1 \ldots k$ | each $\geq 1$ |

With the constraint: $R = r_1 + r_2 + \ldots + r_k \leq M$ (where $M$ is the maximum number of radios).

## Derivation

Let $R = r_1 + r_2 + \ldots + r_k$ (total radios), where $R$ ranges from $k$ to $M$.

For a given $R$:
- $\binom{R-1}{k-1}$ ways to distribute $R$ radios among $k$ types (each $\geq 1$) — stars and bars
- $(R + 1)$ choices for switches ($0$ through $R$)
- $N_{DU}$ choices for DUs ($1$ through $N_{DU}$)

## Generalized Formula ($k$ radio types)

$$\text{Total} = N_{DU} \cdot \sum_{R=k}^{M} \binom{R-1}{k-1} \cdot (R + 1)$$

Where:
- $N_{DU}$ — the number of DU choices ($1 \leq d \leq N_{DU}$, e.g. $N_{DU} = 8$)
- $\binom{R-1}{k-1}$ — the number of ways to distribute $R$ radios among $k$ types with each type having at least 1 (stars and bars)
- $(R + 1)$ — the number of switch choices ($s = 0, 1, \ldots, R$)
- The sum runs over all valid total radio counts $R$ from $k$ (minimum, since each type needs at least 1) to $M$ (maximum)

### Examples for 1 to 5 (and k) radio types

For $k=1$ (1 radio type):

$$\text{Total} = N_{DU} \cdot \sum_{R=1}^{M} (R + 1) = N_{DU} \cdot \frac{M(M+3)}{2}$$

For $k=2$ (2 radio types):

$$\text{Total} = N_{DU} \cdot \sum_{R=2}^{M} (R-1)(R+1) = N_{DU} \cdot \frac{(2M+5)\binom{M}{2}}{3}$$

For $k=3$ (3 radio types):

$$\text{Total} = N_{DU} \cdot \sum_{R=3}^{M} \frac{(R-1)(R-2)}{2} \cdot (R+1) = N_{DU} \cdot \frac{(3M+7)\binom{M}{3}}{4}$$

For $k=4$ (4 radio types):

$$\text{Total} = N_{DU} \cdot \sum_{R=4}^{M} \frac{(R-1)(R-2)(R-3)}{6} \cdot (R+1) = N_{DU} \cdot \frac{(4M+9)\binom{M}{4}}{5}$$

For $k=5$ (5 radio types):

$$\text{Total} = N_{DU} \cdot \sum_{R=5}^{M} \frac{(R-1)(R-2)(R-3)(R-4)}{24} \cdot (R+1) = N_{DU} \cdot \frac{(5M+11)\binom{M}{5}}{6}$$

For arbitrary $k$:

$$\text{Total} = N_{DU} \cdot \sum_{R=k}^{M} \binom{R-1}{k-1} \cdot (R+1) = N_{DU} \cdot \frac{(kM + 2k + 1)\binom{M}{k}}{k+1}$$

## Relation to "n choose k"

The binomial coefficient $\binom{n}{k}$ counts the ways to choose $k$ items from $n$ distinct items. The full problem doesn't reduce to a single $\binom{n}{k}$ because the switch and DU dimensions are simply counting integers in a range.

However, if you **lock** $d$ and $s$ and fix the total $R$, the number of ways to distribute $R$ radios among $k$ types (each $\geq 1$) is:

$$\binom{R-1}{k-1} \quad \text{(stars and bars)}$$

This is the $\binom{n}{k}$ component of the formula. Examples:
- $k=1$: $\binom{R-1}{0} = 1$
- $k=2$: $\binom{R-1}{1} = R - 1$
- $k=3$: $\binom{R-1}{2} = \frac{(R-1)(R-2)}{2}$
- $k=5$: $\binom{R-1}{4}$

The full formula multiplies this by the switch and DU choices and sums over all valid $R$.

## Example

For $M = 120$, $k = 2$: **Total = 585,400 configurations**


# Multiple DU types and switch types

The configuration vector becomes:

$$[d_1, d_2, \ldots, d_p, \; s_1, s_2, \ldots, s_q, \; r_1, r_2, \ldots, r_k]$$

| Group | Types | Constraints |
|-------|-------|-------------|
| DUs | $p$ | each $d_i \geq 0$, total $D \geq 1$, $D \leq N_{DU}$ |
| Switches | $q$ | each $s_j \geq 0$, total $S \leq \min(R, N_S)$ |
| Radios | $k$ | each $r_i \geq 1$, total $R \leq M$ |

Where $N_S$ is the maximum total number of switches.

## Derivation

For a given total $R$:
- $\binom{R-1}{k-1}$ ways to distribute radios (stars and bars, each $\geq 1$)
- $\binom{\min(R, N_S)+q}{q}$ ways to distribute switches (sum over $S=0$ to $\min(R, N_S)$, using hockey stick identity)

For DUs (independent of $R$):
- $\binom{N_{DU}+p}{p} - 1$ ways to distribute DUs (sum over $D=1$ to $N_{DU}$, excluding $D=0$)

## Generalized Formula

$$\text{Total} = \left[\binom{N_{DU}+p}{p} - 1\right] \cdot \sum_{R=k}^{M} \binom{R-1}{k-1} \cdot \binom{\min(R, N_S)+q}{q}$$

**Note:** When $N_S \geq M$ (switches are never the limiting factor), $\min(R, N_S) = R$ and the formula simplifies to:

$$\text{Total} = \left[\binom{N_{DU}+p}{p} - 1\right] \cdot \sum_{R=k}^{M} \binom{R-1}{k-1} \cdot \binom{R+q}{q}$$

For $q=1$ and $N_S \geq M$, the sum further simplifies to the closed form $\frac{(kM+2k+1)\binom{M}{k}}{k+1}$.

Where:
- $p$ — number of DU types
- $q$ — number of switch types
- $k$ — number of radio types
- $N_{DU}$ — maximum total number of DUs
- $M$ — maximum total number of radios
- $\binom{N_{DU}+p}{p} - 1$ — total valid DU distributions (at least 1 DU)
- $\binom{R-1}{k-1}$ — radio distributions for a given total $R$
- $\binom{R+q}{q}$ — total switch distributions for a given total $R$ (from hockey stick: $\sum_{S=0}^{R}\binom{S+q-1}{q-1} = \binom{R+q}{q}$)

### Examples

**Example 1:** $p=1$, $q=1$, $k=2$, $N_{DU}=3$, $N_S=6$, $M=6$

Vector: $[d, s, r_1, r_2]$

$$\text{Total} = \left[\binom{4}{1} - 1\right] \cdot \sum_{R=2}^{6} \binom{R-1}{1} \cdot \binom{R+1}{1} = 3 \cdot 85 = 255$$

**Example 2:** $p=1$, $q=1$, $k=2$, $N_{DU}=1$, $N_S=1$, $M=4$

Vector: $[d, s, r_1, r_2]$ with $d=1$, $s \in \{0, 1\}$

$$\text{Total} = \left[\binom{2}{1} - 1\right] \cdot \sum_{R=2}^{4} \binom{R-1}{1} \cdot \binom{\min(R,1)+1}{1} = 1 \cdot 12 = 12$$

**Example 3:** $p=2$, $q=2$, $k=2$, $N_{DU}=3$, $N_S=6$, $M=6$

Vector: $[d_1, d_2, s_1, s_2, r_1, r_2]$

$$\text{Total} = \left[\binom{5}{2} - 1\right] \cdot \sum_{R=2}^{6} \binom{R-1}{1} \cdot \binom{R+2}{2} = 9 \cdot 295 = 2655$$

