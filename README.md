# 🔬 MetalloSR — Metallurgical Symbolic Regression

MetalloSR is a cluster-guided symbolic regression platform for discovering interpretable mathematical relationships in metallurgical datasets. It uses grammar-constrained PySR searches and compares discovered expressions with a reference-equation library.

## Live Application

**[Launch MetalloSR](https://huggingface.co/spaces/theoRmet/metallosr-equation-discovery)**

The GitHub Pages homepage embeds this application. Its published address is available under the repository’s **Settings → Pages**.

## Workflow

1. Upload a numeric CSV dataset.
2. Enter the target column name.
3. Evaluate the available grammar clusters using PySR.
4. Select a grammar using fit loss, grammar compatibility, and expression complexity.
5. Run the final symbolic regression search.
6. Review candidate equations, fit metrics, and related reference equations.

## Application Workflows

### Auto-Probe

Auto-Probe evaluates the 13 grammar clusters using grammar-constrained PySR searches.

- Each cluster receives the same raw predictor columns.
- The probe uses 300 iterations per cluster without a wall-clock timeout.
- Grammar selection combines normalized loss, grammar compatibility, and expression complexity.
- The selected grammar is used for the final regression run.
- An optional top-three comparison runs final regression for the leading candidate grammars and compares their results.

The fast heuristic probe has been removed. Evaluating all clusters can take time, depending on the dataset and available hardware.

### Select by Reference Equation

If you already know the expected governing physics:

1. Search the reference-equation library.
2. Select a relevant equation.
3. Upload your dataset and enter the target column.
4. Run symbolic regression using that reference equation’s grammar cluster.

Selecting a reference equation selects its grammar; it does not force PySR to reproduce that equation.

## Input Features and Search Design

MetalloSR uses the original numeric predictor columns without automatically constructing inverse-square-root, ratio, logarithmic, or other nonlinear features. No predictor is removed based on its individual correlation with the target.

PySR constructs expressions using the operators allowed by each grammar. Scaling is accounted for when expressions are converted back to the original input and target units.

Grammar operator sets and learned operator weights remain search priors. Removing handcrafted features avoids those particular shortcuts, but does not make every possible equation equally easy to discover.

## Equation Similarity

Discovered expressions are compared with a reference library containing 1,324 equations.

The matcher considers:

- Operator and function usage
- Numeric exponents
- Nested expression and term structure
- Variable count
- Expression complexity
- Reference parsing quality

Matching uses complete expressions without equation-name, variable-name, domain, or partial-expression ranking bonuses. Reference similarity is reported for interpretation and does not select the final regression winner.

When SymPy can parse both expressions, an additional equality check attempts symbolic simplification while preserving variable identities and coefficients.

**Similarity scores are heuristic retrieval scores. They are not probabilities, proof of algebraic equivalence, or evidence that an equation is physically correct.** Complex LaTeX and imperfect reference entries may limit matching quality.

## Results and Interpretation

The application reports:

- Candidate equations
- R², adjusted R², and RMSE
- Expression loss and complexity
- Grammar compatibility
- Raw-input correlation summaries
- Permutation importance
- Similar reference equations

Reported fit metrics are calculated on the data used for fitting; they are not held-out validation results. Adjusted R² does not account for all degrees of freedom introduced by symbolic search.

Grammar compatibility describes operator agreement with a grammar. It is not a calibrated confidence score for scientific correctness.

Validate discovered equations against independent data, dimensional consistency, and relevant physical constraints before drawing scientific conclusions.

## Dataset Format

Upload a CSV file containing:

- A numeric target column
- At least one numeric predictor column
- At least 10 usable rows after filtering

Enter the target column name exactly as it appears in the CSV header.

The current application samples datasets larger than 80 rows to 80 rows using a fixed random seed to limit computation.

## Technology

- Python
- Gradio
- PySR
- SymbolicRegression.jl
- Julia
- SymPy
- pandas
- NumPy
- scikit-learn
- Docker
- GitHub Actions
- GitHub Pages
- Hugging Face Spaces

## Repository Files

| File or folder | Purpose |
|---|---|
| `app.py` | Gradio interface, regression workflows, and reference matching |
| `grammars/` | Grammar cluster definitions |
| `equations_browser.json` | Reference-equation library |
| `requirements.txt` | Python dependencies |
| `Dockerfile` | Application container configuration |
| `Precompile.py` | PySR precompilation helper |
| `docs/index.html` | GitHub Pages homepage embedding the application |
| `.github/workflows/` | Repository automation |
| `README.md` | Project documentation |

## Deployment Architecture

GitHub hosts the source repository and the GitHub Pages entry point. Hugging Face Spaces hosts the interactive Gradio application and its Python, Julia, and PySR runtime.

GitHub Pages serves the static homepage; it does not execute symbolic regression.

```text
GitHub Pages homepage
        ↓
Embedded Gradio application
        ↓
Hugging Face Space
        ↓
Python + Julia + PySR
```

The homepage embeds:

```text
https://theormet-metallosr-equation-discovery.hf.space
```

The direct application link is:

```text
https://huggingface.co/spaces/theoRmet/metallosr-equation-discovery
```

## Development Checks

Focused regression checks cover raw-input preservation, structural distinctions, equivalent notation, scaling conversion, and conservative symbolic equality.

These checks do not establish database-wide matching accuracy or replace end-to-end deployment testing.
