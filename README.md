# **A Bayesian Network to Model the Influence of Energy Consumption on Greenhouse Gases in Italy**

## **Overview**
This project builds a **Bayesian Network** to model the influence of **energy consumption** on **greenhouse gas emissions (CO₂, CH₄, N₂O) in Italy**. The model is constructed using **pgmpy**, a Python library for Probabilistic Graphical Models (PGMs), and is based on **annual growth factors** of various indicators sourced from the **World Bank Group (WBG)**.

The network captures **causal relationships** between **energy use, fossil fuel consumption, renewable energy consumption, GDP, population, and greenhouse gas emissions**. The goal is to **infer probabilistic dependencies** between these factors and analyze how different variables impact greenhouse gas emissions.

---

## **Key Features**
- **Bayesian Network Construction**:
  - Defines causal relationships between **energy use, economic growth, and greenhouse gases**.
  - Implements **Conditional Probability Tables (CPTs)** using **Bayesian Estimation (BDeu prior)**.
  
- **Data Preprocessing**:
  - Converts **raw World Bank data** into **annual growth rates**.
  - **Discretizes continuous data** into three tiers (low, medium, high) for compatibility with **pgmpy**.

- **Probabilistic Inference & Analysis**:
  - Uses **Variable Elimination** to perform **Bayesian inference**.
  - Evaluates the **impact of renewable energy adoption vs. fossil fuel use** on greenhouse gas emissions.
  - Investigates **conditional independencies and active trails** in the Bayesian Network.

---

## **Bayesian Network Structure**
The Bayesian Network consists of the following **nodes** (random variables):

| **Node** | **Description** |
|----------|---------------|
| `Pop`  | Population Growth (Annual %) |
| `Urb`  | Urban Population Growth (Annual %) |
| `GDP`  | GDP per Capita Growth (Annual %) |
| `EC`   | Energy Use per Capita (Annual Growth %) |
| `FFEC` | Fossil Fuel Energy Consumption (% of Total) |
| `REC`  | Renewable Energy Consumption (% of Total) |
| `EI`   | Energy Imports (% of Energy Use) |
| `CO2`  | CO₂ Emissions (Metric Tons per Capita) |
| `CH4`  | Methane Emissions in the Energy Sector |
| `N2O`  | Nitrous Oxide Emissions in the Energy Sector |

### **Graph Structure**
The following relationships are defined in the Bayesian Network:

```
   Pop   → EC   ← Urb
   GDP   → EC
   EC    → FFEC, REC, EI
   FFEC  → CO2, CH4, N2O
   REC   → CO2, CH4, N2O
```

This structure models how **population growth, urbanization, and GDP affect energy consumption**, which in turn influences **fossil fuel and renewable energy use**, ultimately impacting **greenhouse gas emissions**.

---

## **Dataset**
- The dataset is retrieved from the **World Bank Open Data** repository.
- Features are transformed into **annual growth rates** to **capture trends over time**.
- The dataset undergoes **discretization** into three tiers (low, medium, high).

---

## **Implementation Details**
### **1. Bayesian Network Construction**
- A **Directed Acyclic Graph (DAG)** is defined using `pgmpy.models.BayesianModel`.
- Conditional Probability Distributions (CPDs) are computed using `BayesianEstimator` with **BDeu prior**.

### **2. Learning and Inference**
- The model is trained using historical **energy consumption and emissions data**.
- **Inference is performed** using the `VariableElimination` algorithm.

### **3. Querying the Bayesian Network**
- Queries are made to investigate:
  - The impact of **population growth on energy consumption and CO₂ emissions**.
  - The influence of **GDP growth on greenhouse gas emissions**.
  - The effect of **reducing fossil fuel consumption and increasing renewable energy use**.

---

## **Results & Key Findings**
### **1. Population, Urbanization & GDP Impact on Energy Use**
- **Energy consumption is more strongly linked to CO₂ emissions** than to population or GDP growth.
- Industrialization might be an important missing factor.

### **2. Relationship Between CO₂, CH₄, and N₂O**
- **CO₂ and N₂O emissions** follow similar trends.
- **CH₄ emissions show inconsistent trends** due to **data sparsity**.

### **3. Impact of Renewable vs. Fossil Fuels**
- Reducing **fossil fuel use** and increasing **renewable energy adoption** significantly reduces **greenhouse gas emissions**.

---

## **Dependencies**
To run the project, install the required Python libraries:

```bash
pip install numpy pandas pgmpy
```

---

## **Usage**
### **Running the Bayesian Network**
1. **Load the dataset** and preprocess the data:
   ```python
   from pandas import read_csv
   df = read_csv("italy-raw-data.csv")
   ```

2. **Define the Bayesian Network:**
   ```python
   from pgmpy.models import BayesianModel
   model = BayesianModel([('Pop', 'EC'), ('Urb', 'EC'), ('GDP', 'EC'),
                          ('EC', 'FFEC'), ('EC', 'REC'), ('EC', 'EI'),
                          ('REC', 'CO2'), ('REC', 'CH4'), ('REC', 'N2O'),
                          ('FFEC', 'CO2'), ('FFEC', 'CH4'), ('FFEC', 'N2O')])
   ```

3. **Train the Bayesian Network with Bayesian Estimation:**
   ```python
   from pgmpy.estimators import BayesianEstimator
   model.fit(data=df, estimator=BayesianEstimator, prior_type="BDeu", equivalent_sample_size=10)
   ```

4. **Perform Inference (Query CO₂ Emissions Based on Evidence):**
   ```python
   from pgmpy.inference import VariableElimination
   infer = VariableElimination(model)
   result = infer.query(variables=['CO2'], evidence={'EC': 'High'})
   print(result)
   ```

---

## **References**
1. [U.S. Environmental Protection Agency - Greenhouse Gas Emissions](https://www.epa.gov/ghgemissions/overview-greenhouse-gases)
2. [Cinar & Kayakutlu (2010) - Bayesian Networks in Energy Sector](https://www.sciencedirect.com/science/article/pii/S0950705110000110)
3. [World Bank Open Data](https://data.worldbank.org/indicator)

---


