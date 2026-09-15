# Efficiency Wages with an Informal Sector

What happens to the Shapiro–Stiglitz worker-discipline model when the alternative to a
formal job is not an unemployment cheque, but a job in the informal sector?

This project extends **Shapiro & Stiglitz (1984), *Equilibrium Unemployment as a Worker
Discipline Device*** to a dual labour market, solves it analytically, rebuilds it as an
agent-based simulation with heterogeneous workers and firms, runs shock experiments, and
calibrates it against Colombian labour-market data.

> Final project for **Python Programming for Economists**, Tilburg University.

[![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/RobertoStipanovic/efficiency-wages-informality/blob/main/notebooks/efficiency_wages_informality.ipynb)
[![Open in GitHub Codespaces](https://github.com/codespaces/badge.svg)](https://codespaces.new/RobertoStipanovic/efficiency-wages-informality)

---

## The idea

In the canonical model, firms cannot observe effort, so they pay above market-clearing
wages and let unemployment do the disciplining. The threat works because the outside
option — pure unemployment at benefit `w_bar` — is bad and *static*.

In most of the global South it is neither. A fired worker starts selling something the
next morning. That makes the outside option **productive and endogenous**: it improves
exactly when the informal sector does. The no-shirking condition becomes

$$A \alpha L_f^{\alpha - 1} = e + w_{\text{fallback}} + \frac{e}{q}\left(\frac{b}{u_{\text{total}}} + r\right)$$

where `w_fallback` is the expected value of the informal option rather than a fixed
benefit. Because being fired hurts less, formal firms must pay *more* to keep the
discipline mechanism credible.

## What is in the notebook

1. **The base model** — no-shirking condition, labour demand (Cobb–Douglas MPL),
   equilibrium by root-finding, and comparative statics in monitoring intensity `q`,
   separation rate `b`, discount rate `r` and effort `e`, plus a welfare comparison
   against the social optimum.
2. **The dual-sector extension** — informal employment with congestion, the modified NSC,
   and a welfare analysis of the dual economy against both the standard model and the
   first-best allocation.
3. **Agent-based simulation** — 10,000 workers in the base model and 5,000 workers with
   heterogeneous firms in the extension. Workers differ in discount rates `r_i`, firms in
   monitoring intensity `q_j`; each agent decides individually whether to shirk.
4. **Shock experiments** — an unemployment-benefit shock, a productivity shock, and a
   simultaneous multi-shock designed to mimic AI adoption (productivity up, monitoring up,
   separations up).
5. **Empirical calibration** — World Bank series pulled live via `wbgapi` for Brazil,
   Colombia and Mexico, then the model inverted on Colombian data to back out implied
   productivity `A`, benchmarked against official DANE productivity statistics.

## Results

**The discipline mechanism survives heterogeneity.** The agent-based economy converges to
the analytical steady state even though every worker and firm has different parameters:

| | Analytical | Simulated (last 5,000 periods) |
|---|---:|---:|
| Formal employment `L_f` | 82.32% | 82.11% |
| Informal employment `L_i` | 2.44% | 2.38% |
| Pure unemployment `U` | 15.24% | 15.52% |
| Formal NSC wage `w_f` | 0.3671 | 0.3666 |

Micro-level noise averages out; the aggregate prediction is robust.

**Informality has a welfare tipping point.** Whether the informal sector is a safety net
or a parasite depends on its productivity:

- *Destructive informality* — when the informal sector is moderately productive and
  congested, it forces formal wages up and formal employment down, and displaced workers
  produce less than they did before. Net welfare falls.
- *Productive informality* — when a technology shock raises maximum informal productivity
  (think app-based gig work), informal output overwhelms the formal deadweight loss and
  net welfare rises.

This yields a counter-intuitive policy implication, discussed in the notebook: enforcement
against informal work can *raise* formal employment, by lowering the fallback wage and so
lowering the efficiency wage firms must pay.

**The calibration is where the model shows its limits.** Inverted on Colombian data the
model correctly attributes the 2020 collapse in formal employment to a fall in implied
productivity, but its implied productivity series is far more volatile than the official
DANE numbers, and the two decouple over 2011–2015. The model forces every labour-market
adjustment through productivity `A` and search efficiency `lambda_i`, so capital deepening
in extractive industries and terms-of-trade swings — which a labour-only production
function cannot see — end up misattributed.

## Repository layout

```
.
├── notebooks/
│   └── efficiency_wages_informality.ipynb        # the full project
├── data/
│   └── anex-PTF-ProductividadLaboral-2025.xlsx   # DANE productivity annex (Colombia)
├── requirements.txt
└── README.md
```

World Bank indicators are fetched at runtime through `wbgapi`, so they are not stored in
the repository. The notebook uses:

| Code | Series |
|---|---|
| `SL.ISV.IFRM.ZS` | Informal employment rate |
| `SL.UEM.TOTL.ZS` | Unemployment rate |
| `SL.EMP.WORK.ZS` | Wage and salaried workers (formal employment) |
| `SP.POP.1564.TO.ZS` | Working-age population |
| `SL.TLF.CACT.ZS` | Labour force participation |

## Run it online

- **[Open in Google Colab](https://colab.research.google.com/github/RobertoStipanovic/efficiency-wages-informality/blob/main/notebooks/efficiency_wages_informality.ipynb)** —
  runs in the browser with no setup. `wbgapi` installs itself in the first cell and the
  Excel annex is read straight from this repository. To keep your edits, use
  *File → Save a copy in GitHub*.
- **Edit on github.dev** — press <kbd>.</kbd> on any page of this repository for a VS Code
  editor in the browser. Good for editing and committing; it cannot execute cells.
- **[Open in Codespaces](https://codespaces.new/RobertoStipanovic/efficiency-wages-informality)** —
  a full container with a working kernel.

## Run it locally

```bash
git clone https://github.com/RobertoStipanovic/efficiency-wages-informality.git
cd efficiency-wages-informality
python -m venv .venv && source .venv/bin/activate   # Windows: .venv\Scripts\activate
pip install -r requirements.txt
jupyter lab notebooks/efficiency_wages_informality.ipynb
```

Simulation seeds are fixed (`np.random.seed`), so the agent-based results reproduce
exactly. The full run takes a few minutes; the World Bank cells need an internet
connection.

## Known weaknesses

Taken from the notebook's own discussion — these are the honest limits of the exercise:

- **Zero effort cost in the informal sector.** Effort disutility `e` is subtracted for
  formal workers but not for informal ones, which mechanically biases the welfare results
  toward finding an informal premium. An informal disutility `e_inf` and a risk penalty
  are needed.
- **No fiscal wedge.** Unemployment benefits are exogenously funded. In reality formal
  firms pay the payroll taxes that fund them while informal firms evade, which is itself a
  force pushing workers into informality.
- **Labour-only production.** No capital, so every shock is read as a productivity shock.
- **Static parameters in the calibration.** `b`, `q`, `e` and `r` are held fixed across two
  decades of Colombian data, which is a strong assumption over that horizon.

## Sources

- Shapiro, C. & Stiglitz, J. E. (1984). *Equilibrium Unemployment as a Worker Discipline
  Device*. American Economic Review 74(3), 433–444.
- World Bank, World Development Indicators (via `wbgapi`).
- DANE (Colombia), *Anexo PTF y Productividad Laboral 2025*.

## License

[MIT](LICENSE) for the code. The DANE and World Bank data belong to their respective
publishers.
