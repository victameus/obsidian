### Part 1: How the script works

**Stage 1 — Load and figure out the timing grid**

python

```python
STEP_PER_PULSE = (encoding_parameters[3]+encoding_parameters[4])/time_step_array[1]/1000
```

`enc[3]=500` (pulse width ms), `enc[4]=1000` (delay ms), `time_step_array[1]=0.2` s → `(500+1000)/0.2/1000 = 7.5` samples per pulse. Then `NUM_STEP=7`, `EFFECTIVE_NUM_STEP=3`, `START_POINT=2`. This is where your 3-node ceiling comes from.

**Stage 2 — Stack the three wavelengths as three "devices"**

`result_data` becomes `(3, 3755, 8)` — R, G, B are treated as three independent reservoir nodes-sources. Column 2 is I_d.

**Stage 3 — Build the state matrix (this is the conceptual core)**

python

```python
recorded_vector[i][k][j] = result_data[i][START_POINT + pulse_offset + EFFECTIVE_NUM_STEP + k][2] * 1E9
```

Indices: `i` = which wavelength, `k` = which virtual node, `j` = which pulse. For every pulse `j`, it grabs `EFFECTIVE_NUM_STEP` consecutive current samples and calls each one a separate feature. This is **time multiplexing**: one physical device masquerades as many nodes by sampling its relaxation trajectory at several delays.

Let me draw the indexing, since this is the part that's easy to get wrong:

V

visualize

V

visualize show_widget

So after stage 3 you have `recorded_vector` of shape `(3 wavelengths, 3 taps, 500 pulses)` — a 9-dimensional state trajectory. In yours, `EFFECTIVE_NUM_STEP = 3`, so only three dots per window instead of the four drawn above.

**Stage 4 — Build the target**

python

```python
u_input = 0.5 * x_input          # x_input is y_data, uniform [0,1] → u in [0, 0.5]
y_solution = nonlineareqn(u_input)
```

With `TYPE=0` you're running:

y[n]=0.4 y[n−1]+0.4 y[n−1]y[n−2]+0.6 u[n]3+0.1y[n] = 0.4\,y[n-1] + 0.4\,y[n-1]y[n-2] + 0.6\,u[n]^3 + 0.1y[n]=0.4y[n−1]+0.4y[n−1]y[n−2]+0.6u[n]3+0.1

This is NARMA-2 with the input term at lag 0 instead of lag 9. Note `x_data` in the npz is just the index array `0…499` — the actual input sequence is `y_data`, which is what gets passed in. Easy to confuse.

**Stage 5 — Train a linear readout**

python

```python
final_input_vector[i][j*step_point_num+k] = recorded_vector[j][k][i]
```

Flattens (device, tap) into one feature row per pulse, size `device_num × step_point_num`. Then:

python

```python
X_train = X[0:100]; X_test = X[100:130]
ridge = Ridge(alpha=alphaval).fit(X_train, y_train)
```

**This is the whole point of reservoir computing.** The device is never trained. The only trainable parameters are the ridge weights WoutW_{out} Wout​ — one number per virtual node. The readout is a single matrix multiply, which is why RC is cheap and why the physics has to do all the work.

**Stage 6 — Sweep and report**

The `for i in range(DEVICE_NUM_RANGE + 1)` loop tries device 1, 2, 3 individually, then `i=3` = all three combined. Inner loop sweeps `step_point_num` 1→10 (nodes per device). That's the heatmap: rows = channel, columns = node count, cells = NMSE in dB.

python

```python
nmse = 10*np.log10(err_sqrd_sum / value_sqrd_sum)
```

`get_nmse` normalizes by ∑y2\sum y^2 ∑y2; `get_nmse_val` normalizes by variance around the mean. `TYPE=0` returns the first, `TYPE=1` the second. More negative dB = better. −20 dB means residual power is 1% of signal power.

---

### Part 2: What makes a good reservoir

A reservoir has to do exactly one job: **map the input history {u[n],u[n−1],… }\{u[n], u[n-1], \dots\} {u[n],u[n−1],…} into a high-dimensional state x[n]\mathbf{x}[n] x[n] such that the target is a linear function of x[n]\mathbf{x}[n] x[n].** Everything below is a corollary of that.

#### 1. Echo state property (fading memory)

The state must depend on recent input and _forget_ distant input. Formally, the influence of u[n−k]u[n-k] u[n−k] on x[n]\mathbf{x}[n] x[n] must decay to zero as k→∞k \to \infty k→∞. If it doesn't, two things break: identical recent inputs produce different states (so no function can map state→target), and the state distribution drifts, so weights fit on training data don't transfer to test data.

This is your failure mode. Your τ ≈ 21–47 s against a 1.5 s pulse period means u[0]u[0] u[0] still dominates x[499]\mathbf{x}[499] x[499]. The reservoir remembers everything, which is the same as remembering nothing useful.

**How much memory does this task need?** Expand the recursion — y[n]y[n] y[n] depends on y[n−1]y[n-1] y[n−1] with coefficient 0.4, so the influence of input kk k steps back scales roughly as 0.4k0.4^k 0.4k: 40%, 16%, 6.4%, 2.6%. **Effective memory ≈ 3–5 pulses.** So you want τ such that PPC decays substantially over ~1 pulse and is essentially gone by ~5. Target **τ ≈ 1–3 × pulse interval**. Yours is 15–30×.

#### 2. Nonlinearity

A linear reservoir + linear readout = a linear filter, which cannot produce u[n]3u[n]^3 u[n]3 or y[n−1]y[n−2]y[n-1]y[n-2] y[n−1]y[n−2]. The device must nonlinearly transform its input.

Your device does have this — MoS₂ photoresponse vs irradiance is sublinear (trap-limited), and the PPC amplitude vs pulse energy saturates. Good. But the cubic term needs _third-order_ content, so the nonlinearity has to be strong enough. Operating in a saturated regime kills it: if every pulse maxes out the traps, ΔI becomes independent of u[n]u[n] u[n] and you lose the nonlinearity along with the memory.

#### 3. Separability / effective dimensionality

Distinct input histories must map to distinct, **linearly independent** state vectors. Nine features that are 0.99-correlated are one feature with noise. Check this with the singular values of your state matrix — if σ1≫σ2\sigma_1 \gg \sigma_2 σ1​≫σ2​, your effective rank is 1 and ridge has nothing to work with.

The taps only separate if they sample **different decay timescales**. Uniform 0.2 s taps on a 20 s exponential sample essentially the same value three times. This is why log-spaced taps and a genuine multi-τ device matter.

#### 4. The memory–nonlinearity tradeoff

This is the central result of the field (Dambre et al., _Sci. Rep._ 2012): total information processing capacity of a reservoir is bounded by its number of nodes, and it is **split** between linear memory and nonlinear transformation. Push toward strong nonlinearity (drive hard, saturate) and memory collapses; push toward long linear memory and nonlinearity vanishes.

NARMA needs both. So your job is finding the operating point — V_G, irradiance, pulse interval — that balances them. That's not a nuisance, that _is_ the experiment, and it's exactly the axis your paper should be arguing about.

#### 5. Why multiple wavelengths should help (and don't yet)

The reason R/G/B is interesting is **timescale heterogeneity**: different photon energies access different trap depths (your Arrhenius data — 0.061 / 0.359 / 0.389 eV), so each channel has a different τ. A reservoir with mixed fast and slow nodes covers a wider range of memory depths from the same substrate — fast nodes handle u[n]3u[n]^3 u[n]3, slow nodes carry y[n−1],y[n−2]y[n-1], y[n-2] y[n−1],y[n−2].

But that only pays off if the τ's are actually separated _and_ the channels aren't just scaled copies. Right now G (0.359 eV) and B (0.389 eV) are nearly degenerate, and all three are stuck in the integrating regime, so the "combined" row can't beat the single-channel rows.

#### 6. Reproducibility and SNR

The readout is fit on training pulses and applied to test pulses. Any slow change in the device between the two — drift, degradation, self-heating, adsorbate effects — appears directly as test error. This is also what makes your D2–D5 reproducibility data meaningful: RC only works if the reservoir is a stationary function of the input.

---

**The diagnostic checklist**, in the order I'd apply it to new data:

|Property|How to check|Target|
|---|---|---|
|Fading memory|baseline drift end vs start|< 20%|
|Memory depth|corr(node, u[n−1]), u[n−2]|0.4–0.7, then decaying|
|Nonlinearity|fit ΔI vs u[n] with a cubic — is the cubic term significant?|yes|
|Dimensionality|singular values of state matrix|σ₂/σ₁ > 0.1|
|Channel diversity|cross-correlation matrix|< 0.8 off-diagonal|
|SNR|repeat the same pulse 20×|ΔI_noise ≪ ΔI_signal|

If those pass, the NARMA number takes care of itself.