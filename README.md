# FIFA 21 Dataset Engineering, Cleaning, and Dimensionality Reduction Analysis

This repository documents a professional, production-grade data science pipeline designed to ingest, clean, impute, and visualize the FIFA 21 player dataset (`18,979` rows, `77` columns). The pipeline applies robust custom ETL transformations to handle noisy and unstructured columns, normalizes the processed attributes, and performs Dimensionality Reduction via **Principal Component Analysis (PCA)** to cluster players in 2D and 3D spaces based on their physical and technical attributes.

---

## 📂 Project Directory Structure
All relevant files are located within the **Project1_dm** directory:
* **[SourceCode.ipynb](Untitled12.ipynb)**: The primary Jupyter Notebook containing the end-to-end Python pipeline, analysis, visualizations, and interactive plots.
* **[untitled12.py](SourceCode.py)**: The standalone Python script compiled from the notebook.
* **[fifa21_raw_data_v2.csv](fifa21_raw_data_v2.csv)**: The raw, unprocessed source dataset containing player profiles.
* **[FIFA 21 Player Dataset Analysis Report.pdf](FIFA%2021%20Player%20Dataset%20Analysis%20Report.pdf)**: The comprehensive final analytical report of the project.

---

## ⚙️ Data Engineering & Analytical Pipeline

### 1. Ingestion & Preliminary Exploration
* **Observations Count:** `18,979` players.
* **Features Count:** `77` columns.
* **Syntactic Consistency:** Normalization of all column headers to lowercase and conversion of space characters to underscores (`snake_case`) to ensure programmatic compliance.

### 2. Custom Feature Wrangling & Cleaning
To handle highly noisy raw data, custom parsing algorithms were implemented:
* **Height Standardization (`height`):** Converts mixed imperial formatting (e.g., `5'11"`) into metric Centimeters (`cm`) by parsing feet and inches:
  $$\text{Height (cm)} = \text{feet} \times 30.48 + \text{inches} \times 2.54$$
* **Weight Standardization (`weight`):** Converts imperial weight strings (e.g., `185lbs`) to metric Kilograms (`kg`) by eliminating non-numeric suffixes and dividing by the standard conversion factor `2.205`.
* **Financial Feature Conversion (`value`, `wage`, `release_clause`):** Strips the Euro sign (`€`) and parses abbreviations to float values:
  * `M` (Millions) is multiplied by $1,000,000$.
  * `K` (Thousands) is multiplied by $1,000$.
* **Hits Standardization (`hits`):** Parses string representations (e.g., converting `1.2K` to `1200`) and casts them to integer datatypes.
* **Special Unicode Parsing:** Removes special star characters (`★`) from qualitative columns (e.g., Weak Foot `w/f`, Skill Moves `sm`, International Reputation `ir`) to convert them to clean integers. Strips carriage returns (`\n`) and leading/trailing whitespace from string fields like `club`.

### 3. Missing Data Treatment & Imputation Matrix
* **Feature Pruning:** Removed columns with a non-null ratio below **75%**. Consequently, `loan_date_end` (containing only $1,013$ non-null observations out of $18,979$, or over $94.6\%$ missing values) was dropped.
* **Numerical Imputation:** Missing numerical entries were imputed using the column **Median** to prevent outlier skew.
* **Categorical Imputation:** Missing categorical entries were imputed using the column **Mode** (most frequent class).
* **Validation:** Asserted that the sum of null values across the entire cleaned dataset is exactly `0`.

### 4. High-Dimensional Scaling & PCA Reduction
To map physical and skill metrics into lower dimensions, the pipeline isolates `53` numerical attributes, excluding metadata identifiers (such as `id`, `name`, `longname`, `photourl`, `playerurl`, `nationality`, `club`, `joined`, `contract`, `preferred_foot`, `best_position`, `jersey_number`).

* **Normalization:** Applied `StandardScaler` to center features around a mean of $0$ and scale to unit variance ($1$) to avoid dominance by variables with larger numerical ranges.

#### 📊 2D PCA Model Results:
* **Principal Component 1 (PC1) Explained Variance:** `45.70%`
* **Principal Component 2 (PC2) Explained Variance:** `14.55%`
* **Cumulative Explained Variance (2D):** `60.25%`
* **Visualization:** Plotted using Seaborn's scatterplot interface, revealing distinct spatial clustering of players that naturally corresponds to their tactical roles (`best_position`).

#### 📊 3D PCA Model Results:
* **Cumulative Explained Variance (3D):** `77.72%` (retaining an additional `17.47%` of the information compared to the 2D model).
* **Visualization:** Generated an interactive 3D scatter plot using `plotly.express` (`scatter_3d`), enabling hover tooltips showing player names, positions, and PCA scores, alongside zoom and spatial rotation features.
