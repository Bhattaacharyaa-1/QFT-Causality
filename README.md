# QFT-Causality
# Causal Inference Methods for Quantitative Finance

---

## 1. Methods for Trading Strategy Evaluation & Execution

### A. Dynamic Position Sizing & Time-Varying Confounding
* **Book Concept:** **Dynamic Treatment Strategies & G-computation** (Chapters 8 & 17)
* **Trading Application:** Trading strategies are rarely static; they dynamically adjust position sizes based on evolving market conditions (e.g., volatility targeting, dynamic hedging). A major pitfall is **time-varying confounding**—where past market states affect both your current position size and future returns. Standard regression fails here.
* **Method:** Use Robins' **G-computation algorithm** or **Marginal Structural Models (MSMs)** to simulate the counterfactual outcomes of dynamic trading rules, properly adjusting for the history of market states and past PnL.

---

### B. Market Regimes & The Decision-Theoretic Approach
* **Book Concept:** **Decision-Theoretic Approach & Regime Indicators** (Chapter 4)
* **Trading Application:** A strategy might work in a low-volatility bull market but fail in a high-volatility crisis.
* **Method:** Introduce a non-random **Regime Indicator ($\sigma$)** (e.g., $\sigma \in \{\text{Risk-On}, \text{Risk-Off}, \text{High-Vol}\}$) into your causal Directed Acyclic Graph (DAG). This allows you to formally define and test the *modularity* assumption: does the causal effect of your Alpha factor on returns remain invariant across different market regimes?

---

### C. Time-Series Causality & Lead-Lag Effects
* **Book Concept:** **Causal Inference in Time Series** (Chapter 22)
* **Trading Application:** Discovering true lead-lag relationships between assets, order flow, and prices, rather than mere cointegration or correlation.
* **Method:** Move beyond standard correlation by applying **Granger Causality**, **Sims Causality**, and **Time-Series Path Diagrams**. Use the **Graphical Back-Door Criterion** for time series to isolate the true directional flow of information (e.g., does order book imbalance *cause* price movement, or do anticipated price movements *cause* order book quoting behavior?).

---

### D. Natural Experiments & Instrumental Variables
* **Book Concept:** **Natural Experiments & Instrumental Variables** (Chapters 4.6 & 18)
* **Trading Application:** Proving that a specific market microstructure feature or macroeconomic event *causes* an asset price move, ruling out reverse causation or hidden confounders.
* **Method:** Use exogenous market shocks (e.g., sudden index rebalancing, unexpected regulatory announcements, or hardware outages at a major exchange) as **Natural Experiments**. Alternatively, use **Instrumental Variables (IV)** (e.g., using the latency of a competing exchange's feed as an instrument for local order flow toxicity) to isolate the causal impact of liquidity on returns.

---

## 2. Methods for Data Engineering & Alpha Factor Construction

### A. Causal Graphs for Feature Selection (Avoiding Collider Bias)
* **Book Concept:** **Structural Equations, Graphs, and Interventions** (Chapters 3 & 6)
* **Trading Application:** In data engineering, blindly feeding all available features into a machine learning model often induces **collider bias** or **M-bias**, creating spurious Alpha that vanishes live.
* **Method:** Construct a **Causal DAG** of the market based on financial theory (e.g., $\text{Macro Factors} \rightarrow \text{Sector Factors} \rightarrow \text{Stock Returns} \leftarrow \text{Idiosyncratic Noise}$). Use **do-calculus** and the **Back-Door/Front-Door criteria** to determine the exact minimal set of variables you must condition on to isolate the true causal effect of a feature on future returns, deliberately blocking back-door paths (confounders) while avoiding conditioning on colliders (e.g., conditioning on both a stock's return and its sector's return).

---

### B. Mediation Analysis for Factor Decomposition
* **Book Concept:** **The Mediation Formula & Direct/Indirect Effects** (Chapters 11 & 12)
* **Trading Application:** Understanding *how* an Alpha factor works. If a "Sentiment" factor predicts returns, is it because it directly captures informed trading (Direct Effect), or does it merely predict short-term liquidity provision which then moves the price (Indirect Effect via a mediator)?
* **Method:** Apply Pearl's **Mediation Formula** to decompose total factor returns into **Natural Direct Effects** and **Natural Indirect Effects**. This allows data engineers to orthogonalize factors correctly and understand the true economic mechanism driving the signal.

---

### C. Sensitivity Analysis for Hidden Market States
* **Book Concept:** **Sensitivity Analysis & Unmeasured Confounding** (Chapter 19)
* **Trading Application:** Financial markets always have "hidden states" (e.g., dark pool inventory, unrecorded macroeconomic fears) that cannot be directly measured but confound your data.
* **Method:** Apply Rosenbaum’s **Sensitivity Analysis for Observational Studies**. Instead of just reporting a Sharpe Ratio, calculate the **Design Sensitivity ($\tilde{\Gamma}$)**: *How strong would an unmeasured hidden market factor need to be to completely explain away the causal edge of my trading strategy?* This provides a rigorous mathematical bound on the robustness of your data pipeline.

---

### D. Modeling Market Microstructure as Dynamic Systems
* **Book Concept:** **Dynamic Molecular Networks & Stochastic Kinetic Models (SKMs)** (Chapter 23)
* **Trading Application:** Limit Order Books (LOB) and matching engines behave like complex, dynamic, stochastic reaction networks (e.g., limit orders "binding" and "canceling" like chemical reactions).
* **Method:** Use **Kinetic Independence Graphs** to model the local independence and causal influence of different order types. This allows data engineers to build highly realistic **synthetic market simulators** for stress-testing strategies, ensuring that the simulated order flow respects the fundamental causal mechanics of the exchange's matching engine.

---

## Summary Paradigm Shift for Quant Finance

By applying the methods from this book, a quantitative team shifts its data engineering and strategy evaluation pipeline:

* **From (Associational / Observational):**  
  > *"Feature $X$ has a high Information Coefficient (IC) with tomorrow's return."*

* **To (Causal / Interventional):**  
  > *"Intervening to trade based on Feature $X$ yields a causal return of $Y$, conditional on our market regime DAG, after blocking macro-confounders via the back-door criterion, and this effect is robust to unmeasured liquidity shocks up to a sensitivity bound of $\Gamma = 2.5$."*
