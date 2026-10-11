# 🏦 Queue Simulation with Two Counters

**Discrete-event simulation of M/D/2, M/M/2 and Tunisian-style queue systems**

Course project for *Stochastic Modeling & Simulation*, Manouba School of Engineering (Université de la Manouba), academic year 2025–2026.

- **Authors:** Nour Abbouz & Zeineb Hachani
- **Supervisor:** Prof. Fatine Magherbi

---

## 📖 Overview

This project studies how **service-time variability** and **queue structure** affect congestion in a service system with two counters. Three configurations are simulated with identical Poisson arrivals (λ = 1 customer/min) over a 120-minute horizon, using Python and the **SimPy** library. An interactive **Streamlit dashboard** lets you explore each system and compare them.

| Feature | M/D/2 | M/M/2 | Tunisian-style |
|---|---|---|---|
| Queue structure | 1 shared queue | 1 shared queue | 2 separate queues |
| Servers | 2 | 2 | 2 (one per queue) |
| Arrivals | Poisson, λ = 1/min | Poisson, λ = 1/min | Poisson, λ = 1/min |
| Service time | Deterministic (1 min) | Exponential (mean 1 min) | Exponential (mean 1 min) |
| Routing | FCFS | FCFS | Join the shortest queue (no switching) |
| Utilization ρ | 0.5 | 0.5 | 0.5 |

The **Tunisian-style** system models a common real-world setup where each counter has its own line. Customers join the shorter line on arrival (a coin flip breaks ties) and cannot switch afterwards.

## ❓ Research Questions

1. What are the average and maximum queue lengths?
2. What are the average and maximum waiting times?
3. What fraction of time are both servers busy, one busy, or both idle?
4. How does service-time variability (deterministic vs. exponential) change these metrics at equal load?
5. How does queue structure (centralized vs. decentralized) change congestion at equal load?

## 🛠️ Method

- **Framework:** discrete-event simulation with SimPy (arrival and departure events, customers modelled as processes requesting a server resource).
- **Random variates:** inverse-transform method, X = −ln(U)/rate, with U ~ Uniform(0, 1).
- **Reproducibility:** fixed random seed (`42`).
- **Metrics:** time-weighted average queue length, maximum queue length, average and maximum waiting time, and server-state proportions (P0 both idle, P1 one busy, P2 both busy).
- **Validation:** simulation compared with theory (Erlang-C for M/M/2, an approximation for M/D/2, independent M/M/1 lanes for the Tunisian system) and checked with Little's Law.

## 📊 Results (λ = µ = 1, T = 120 min, seed 42)

| Metric | M/D/2 | M/M/2 | Tunisian |
|---|---|---|---|
| Customers served | 113 | 125 | 111 |
| Avg. waiting time (min) | 0.167 | 0.435 | 0.180 |
| Max. waiting time (min) | 1.219 | 5.067 | 4.259 |
| Avg. queue length | 0.296 | 0.472 | 0.273 |
| Max. queue length | 3 | 5 | 3 |
| Utilization ρ | 0.5 | 0.5 | 0.5 |

### Theory vs. simulation

| Metric | M/D/2 theory | M/D/2 sim | M/M/2 theory | M/M/2 sim | Tunisian theory | Tunisian sim |
|---|---|---|---|---|---|---|
| W̄q (min) | 0.083 | 0.167 | 0.333 | 0.435 | 1.000 | 0.180 |
| L̄q | 0.083 | 0.296 | 0.333 | 0.472 | 0.500 | 0.273 |
| P0 (%) | 33.3 | 34.3 | 33.3 | 27.0 | 25 | 32.7 |
| P1 (%) | 33.3 | 36.8 | 33.3 | 32.7 | 50 | 38.9 |
| P2 (%) | 33.3 | 28.5 | 33.3 | 40.0 | 25 | 28.4 |

### Key findings

- **Variability is costly.** At the same utilization, exponential service (M/M/2) more than doubles the average wait compared with deterministic service (M/D/2), and the maximum wait grows from about 1.2 to 5.1 min.
- **M/M/2 has the heaviest waiting-time tail**, reaching about 5 min. The Tunisian system reaches about 4.3 min, while M/D/2 stays concentrated near zero.
- **Queue structure:** within this single 120-minute run, the Tunisian system's *average* wait (0.18 min) is lower than M/M/2's (0.43 min). Its weakness shows in the **maximum wait and lane imbalance**, caused by the no-switching rule. Over a longer horizon, the pooling advantage of the centralized M/M/2 queue is expected to emerge.
- **Simulation vs. theory:** results agree in ranking, but a 120-minute horizon includes transient behaviour and the Tunisian theory assumes perfectly balanced lanes, which explains the gaps.

## 🖥️ Interactive Dashboard

The Streamlit app ("Queueing Sim") provides:

- **Sidebar controls:** arrival rate λ, service rate µ, simulation time, random seed, and an automatic stability check (ρ < 1).
- **KPI cards:** customers served, average and maximum wait, average and maximum queue, utilization.
- **Per-system dashboards:** queue length over time, busy servers over time, waiting time per customer, waiting time histogram, and time-weighted server state (both idle / one busy / both busy).
- **Comparison view:** overlaid waiting-time distributions, queue length over time for all systems, and bar charts of the key metrics.

## 🖼️ Screenshots

> Save your screenshots in a `docs/` folder and adjust the file names.

| Tunisian system | M/D/2 system |
|---|---|
| ![Tunisian](docs/tunisian_dashboard.png) | ![M/D/2](docs/mdc2_dashboard.png) |

| M/M/2 system | Comparative metrics |
|---|---|
| ![M/M/2](docs/mm2_dashboard.png) | ![Comparison](docs/comparative_metrics.png) |

| Waiting-time distributions and queue length over time |
|---|
| ![Overlay](docs/overlay.png) |

## 🚀 Getting Started

```bash
git clone https://github.com/Zeineb19/Stochastic_Project.git
cd Stochastic_Project

python -m venv .venv
source .venv/bin/activate        # Windows: .venv\Scripts\activate

pip install -r requirements.txt  # simpy, numpy, matplotlib, plotly, streamlit
streamlit run app.py
```

> Adjust `app.py` if your Streamlit script or notebook has a different name.

## 🧰 Tech Stack

Python · SimPy · NumPy · Matplotlib · Plotly · Streamlit · Jupyter Notebook · LaTeX (report)

## ⚠️ Limitations and Future Work

- **Single replication:** results come from one sample path (seed 42) and are illustrative, not statistically definitive. Multiple replications with confidence intervals are needed.
- **Finite horizon:** 120 minutes captures transient behaviour; a warm-up period would give steady-state estimates.
- **Fixed load:** only ρ = 0.5 was studied; higher utilizations (0.8–0.9) would widen the gaps between systems.
- **Possible extensions:** jockeying (queue switching) in the Tunisian system, and Erlang-k or log-normal service times.

## 📚 References

- Kendall (1953), stochastic processes in queueing theory
- Erlang (1917), telephone-exchange probability problems
- Little (1961), proof of L = λW
- Kleinrock (1975), *Queueing Systems, Vol. 1*
- Banks et al. (2010), *Discrete-Event System Simulation*
- Law (2015), *Simulation Modeling and Analysis*
- Winston (1977), optimality of the shortest line discipline
- SimPy Development Team (2023), SimPy documentation

## 📄 License

Add a license of your choice (e.g. MIT).
