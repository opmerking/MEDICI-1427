# MEDICI-1427

**Model for Early Dynamics and Interactions in Commercial Italy**

This repository explores household wealth and inequality in Renaissance Florence. To do this, an agent-based model simulates economic dynamics at a household-level from 1427 to 1457, drawing on historical datasets from the *Catasto* tax records of 1427 and 1457, along with loan and mortality records, to replicate wealth distribution trends, trade interactions, and demographic shifts over time.  Additionally, a Bayesian network has been constructed over the 1427 records to examine household characteristics and taxable wealth.

*These analyses were conducted for the Research Methods for Artificial Intelligence and Bayesian Reasoning and Learning courses at the Open University of the Netherlands.*

## Agent-based simulation

The simulation uses Mesa to initialize household agents from 9,780 records in the 1427 *Catasto*. Households produce economic value, trade, manage debt, and pay taxes. The model also includes population changes and household splitting. Historical forced-loan and mortality records are used to represent economic disruption associated with wars and epidemics.

### Setup

**Simulation dependencies:**

```bash
pip install pandas numpy matplotlib seaborn mesa==3.2.0
```

Run the simulation from the repository root:

```bash
python run.py
```

This runs the simulation for 30 steps (1427-1457) and generates plots of the Gini coefficient evolution (indicating inequality), average household wealth, and simulated versus historical wealth distributions.

### Results

| Measure | Simulated | Historical (1457) |
|---|---:|---:|
| Gini coefficient | 0.644 | 0.634 |
| Population | 32,149 | 31,964 |
| Households | 7,547 | 7,455 |

Across three random seeds, the final Gini coefficient averaged 0.644 with a standard deviation of 0.002, approximately 1.6% above the historical value. Population dynamics and household splitting were calibrated using historical demographic totals, so their close agreement is expected.

![Gini coefficient over time](output_examples/Gini.png)

Despite the close Gini coefficient, the simulation substantially overestimated wealth levels. The plot below shows how simulated average household wealth changed over time, alongside war and epidemic years.

![Average household wealth over time](output_examples/avg_wealth.png)

The model uses simplified financial rules and an approximate representation of economic shocks. Further calibration to wealth data and comparison against additional historical years would help assess its generalizability.

### Model parameters

| Parameter | Value | Basis |
|---|---|---|
| Simulation period | 30 years (1427–1457) | Interval between the two Catasto datasets |
| Population trajectory | 38,269 × exp(−0.0058084 × (year − 1427)) | Calibrated to historical population totals |
| Labor productivity | 40 florins/person/year | Based on an estimate of unskilled worker wages |
| Capital return rate | 6% | Pre-industrial return estimate |
| Annual birth rate | 0.5% | Based on the medieval Italian average |
| Epidemic penalty | 1.5 | Assumed multiplier for the economic instability calculation |
| Household splitting probability | Household size² × 0.00015 per year | Calibrated to match the 1457 household count |

## Bayesian network analysis

A separate analysis uses a nine-variable Bayesian network to explore relationships between household characteristics and taxable wealth in the 1427 *Catasto*. Conditional probability tables are learned from the data, and inference is performed using lazy propagation in pyAgrum. 

Two example queries were used to examine taxable wealth among members of the *Arti Maggiori*, Florence's major trade guilds, and widowed women who owned their homes. The model associates major-guild membership with a shift toward higher taxable wealth.

Full methodology and results can be found in the [report](bayesian_inference_report.pdf). The implementation is in [bayesian_inference_code.ipynb](bayesian_inference_code.ipynb). 

## Data sources

- **1427 Catasto:** _[Online Catasto of 1427](https://cds.library.brown.edu/projects/catasto/overview.html)_. Version 1.3. Edited by David Herlihy, Christiane Klapisch-Zuber, R. Burr Litchfield and Anthony Molho. [Machine readable data file based on D. Herlihy and C. Klapisch-Zuber, _Census and Property Survey of Florentine Domains in the Province of Tuscany, 1427-1480._] Florentine Renaissance Resources/STG: Brown University, Providence, R.I., 2002. The data was obtained from Belloc, Drago, Fochesato and Galbiati, [_Data and Code for: Multigenerational Transmission of Wealth: Florence 1403–1480_](https://doi.org/10.3886/E184402V1), licensed under [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/).
- **1457 Catasto:** _[Online Florentine Catasto of 1457](https://doi.org/10.3886/E192821V1)_, available under [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/).
- **Mortality:** Morrison, Kirshner and Molho, “[_Epidemics in Renaissance Florence_](https://doi.org/10.2105/AJPH.75.5.528),” American Journal of Public Health 75(5), 528–535, 1985.
- **Forced loans:** Anthony Molho, *Florentine Public Finances in the Early Renaissance, 1400–1433* (1971), pp. 10, 62; Elio Conti, *L’imposta diretta a Firenze nel Quattrocento, 1427–1494* (1984), pp. 81, 83.

Special thanks to the Brown University Digital Humanities team and to the Sapienza Università di Roma team for making the Catasti data publicly available.
