# R&D Assignment – Curve Fitting

This is my submission for the R&D internship assignment given by FlamAI. The task was to figure out three unknown values - theta, M, and X — that were used to generate a set of points lying on a parametric curve.

The equation given was:

- x(t) = t·cos(θ) − e^(M|t|)·sin(0.3t)·sin(θ) + X
- y(t) = 42 + t·sin(θ) + e^(M|t|)·sin(0.3t)·cos(θ)

with t ranging between 6 and 60, theta between 0–50°, M between −0.05 and 0.05, and X between 0 and 100.

I was given 1500 (x, y) points in xy_data.csv, sampled from this curve, and had to recover theta, M, and X just from those points.

## How I approached it

First I plotted the raw data to see what the curve actually looked like, it turned out to be a smooth curve with a slight wiggle running through it, which matched the sin(0.3t) term in the equation.

Since I didn't know which value of t each point corresponded to, I couldn't fit t directly for every point. So instead, for any guess of theta, M, and X, I generated dense version of the curve using thousands of t values, and then measured how far each real data point was from the closest point on that generated curve. Adding up all these distances gave me one single number,the loss — that tells me how good a particular guess is.

I used scipy's differential_evolution to search across the whole allowed range of theta, M, and X and find the combination that gave the lowest loss, since this avoids getting stuck in a wrong local answer. After that, I ran a local refinement step using Nelder-Mead to polish the result further.

The optimizer converged very close to theta = 30°, M = 0.03, X = 55 — nice round numbers, which usually means these are the actual values used to generate the data rather than just an artifact of the optimization. I double checked this by plugging in the exact clean values and comparing the loss to the optimizer's raw output, they came out almost identical, which confirmed the answer.

I also plotted my fitted curve on top of the real data, and they overlapped almost perfectly. As one more check, I typed the final equation into the Desmos calculator they linked in the assignment and confirmed the shape matches the original data exactly.

## Final answer

theta = 30°, M = 0.03, X = 55

In the LaTeX format asked for:

\left(t*\cos(0.523599)-e^{0.03\left|t\right|}\cdot\sin(0.3t)\sin(0.523599)+55.0,42+t*\sin(0.523599)+e^{0.03\left|t\right|}\cdot\sin(0.3t)\cos(0.523599)\right)

## Desmos verification

Below is a screenshot from Desmos showing the curve rendered with these values, matching the original data shape:

<img width="1710" height="677" alt="Desmos_graph" src="https://github.com/user-attachments/assets/59ee7ce0-5b74-4fe5-9c30-89db528c9584" />


My Desmos graph: https://www.desmos.com/calculator/w9qfrlgwsc

## Files in this repo

- notebook.ipynb — all the code, with outputs already run and saved so you don't need to re-run anything
- xy_data.csv — the data given in the assignment
- desmos_verification.png — screenshot confirming the fit matches visually
