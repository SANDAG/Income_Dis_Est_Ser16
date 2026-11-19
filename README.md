# Income_Dis_Est_Ser16

## 1. Project Objective 

This repository prepares Estimates of the distribution of household level incomes in the San Diego region for the Series 16 Report.  Specifically, it produces predictions of the number of households in each of 14 income brackets that are then aggregated into 5 brackets for the purpose of simulation by Forecasts and Estimates.  

The repository contains a collection of R Markdown workbooks, design to create a reproducible data-processing workflow that can be easily replicated for future forecasts.  Another goal is to test the predictive power of previous estimations by using a using a Chi-squared goodness of fit test. 
 

**Development principles:**

- Process each dataset independently before combining datasets.
- Use shared configuration files for input/output directories; avoid hardcoded local paths and `setwd()`.
- Preserve existing analytical methods from previous iterations, document and review methodological changes separately. 
- Include data validation checks in every processing notebook.
- Keep original source data and generated outputs outside Git.
- Make small, logical commits and use pull requests for review.
- Document input sources, transformations, output schemas, and outstanding assumptions.
- Assign responsibility for production and any internal QA as well as proposed deadlines, update as needed. 

## 2. Repository Structure

Current and planned structure:

```text
Income_Dis_Est_Ser16/
├── notebooks/
│   ├── 01_ipums.Rmd     # Planned Cleans the IPUMS data produces a usable CSV for future steps. 
│   ├── 02_full_model.Rmd      # Planned Produces forecasts based on the full model (all variables for all brackets) ideally export results to an excel file automatically. 
│   ├── 03_bespoke_model.Rmd  # Planned uses the results from the full model and reruns each logit with only statistically significant variables, confirms by comparing F test and R squared
│   ├── 04_ppo_logit.Rmd     # Planned produces a partial parallel ordered logit. Uses a modified Wald test to determine which variables should use parallel trends. 
│   ├── 05_reproduction.Rmd  # Planned reproduces the series 15 estimates and compares the predicted 2024 distribution to the observed means from IPUMS
|   ├── 06_model_selection.Rmd # Planned, uses retroactive data to test which model has best predictive power
│   └── 06_final_dataset.Rmd   # Planned outputs final predicitons for Estimates and Forecasts
├── R/
│   └── load_config.R
├── config/
│   ├── default.yml
│   └── local.yml               # Local only; excluded from Git
├── docs/                       # Planned: data dictionary and methods
├── tests/                      # Planned: automated validation
├── data/
│   ├── raw/                    # Excluded from Git
│   └── processed/              # Excluded from Git
├── .gitignore                  # Tells github which files to ignore (local.yml. raw and processed data)
├── Income_Dis_Est_Ser16.Rproj
└── README.md
```
Notebooks should document each analytical stage. As processing becomes more complex, reusable functions should be moved into `R/` so they can be tested independently. 

## 3. Development Status and Next Steps

### Phase 1 — IPUMS Socioeconomic Data

**Status: Completed for 1 year ACS, 5 year ACS requires additional handling for inflation adjustment and unique HH Identifiers. Highest-priority new development task.**

Notebook: `notebooks/01_ipums.Rmd`

Outputs: `IPUMS_HH_2019_ACS1Y.csv` `IPUMS_HH_2022_ACS1Y.csv` `IPUMS_HH_2024_ACS1Y.csv` `IPUMS_HH_2019_ACS5Y.csv`  `IPUMS_HH_2022_ACS5Y.csv` `IPUMS_HH_2024_ACS5Y.csv` 

Completed:
- [X] Load IPUMS metadata and microdata for 1 year ACS.
- [X] Load IPUMS metadata and microdata for 5 year ACS. 
- [X] Filter to San Diego County.
- [X] Construct household level data (Children, Senior, Age).
- [X] Filter data to head of household level.
- [X] Adjust incomes to 2022 base year  
- [X] Create income catagory varriables (Ologit, Mlogit)
- [X] Create separate binary indicator income category variables
- [X] Export CSVs and verify successful read-back.
- [X] Review IPUMS person/household weighting assumptions.
- [X] Create IPUMS observed averages for 2019, 2022, 2024
- [X] Create IPUMS observed HH per income bracket for 2019,2022,2024
- [X] Review for any missing values.
      
Remaining:


- [ ] Add basic data-quality checks. (partial)
- [ ] Compare outputs against the legacy workflow.
- [ ] Document variable definitions, temporal coverage, and known limitations.

### Phase 2 — Full model

**Status: Partially started in old code. Second Highest-priority new development task.**

Source: Section 3 of `ipums_acs_1year.csv` `ipums_acs_5year.csv`

Planned notebook: `notebooks/02_full_model.Rmd`

Expected output: `full_model.xlsx` May also produce a an intermediate file saving the R models for future prediction 

Completed:
- [X] Create complex survey design.
- [X] Create loopable model for regressions over income catagories.
- [X] Load IPUMS csv data.
- [X] Filter to San Diego County, and year = 2024.
- [X] create complex survey design.
- [X] Input predicted values for independent variables and output predicted percentages.
- [X] Row sum the probabilities to ensure they are close to 1, fall back to mlogit if not.
- [X] Export the predicted probabilities in an csv format.  
      
Remaining:
 
- [ ] Document variable definitions, temporal coverage, and known limitations.

### Phase 3 — Bespoke model

**Status: Not started. Third Highest-priority new development task.**

Source: Section 3 of `ipums_acs_1year.csv` `ipums_acs_5year.csv`

Planned notebook: `notebooks/03_bespoke_model.Rmd`

Expected output: `bespoke_model.xlsx` May also produce a an intermediate file saving the R models for future prediction 

Completed
- [X] create complex survey design.
- [X] Create loopable model for regressions over income catagories.-
- [X] Load IPUMS csv data.
- [X] For each income category, rerun the model with only the statistically signficiant varriables.
- [X] Compare the bespoke and full form R values for goodness of fit, and run an F test on if full form model coefficents are jointly signficant.
- [X] After selecting final models, input predicted values for independent variables and output predicted percentages.
- [X] Row sum the probabilities to ensure they are close to 1.
      
Remaining:
- [ ] Export the predicted probabilities in an excel format.  
- [ ] Document variable definitions, temporal coverage, and known limitations.

### Phase 4 Partial Proportional Odds (PPO Logit) model

**Status: Partially started as old code. Fourth Highest-priority new development task.**

Source: Section 3 of `ipums_acs_1year.csv` `ipums_acs_5year.csv`

Planned notebook: `notebooks/04_ppo_logit.Rmd`

Expected output: `ppo_model.xlsx` May also produce a an intermediate file saving the R models for future prediction 

Completed:
- [X] Load IPUMS csv data.
- [X] create complex survey design. 
- [X] Create a visual test with point estimates and coefficient bars to determine which variables to select as parellel or model specific.
- [X] Create a modified Wald test that incorporates weights from the complex survey design
- [X] After selecting final models, input predicted values for independent variables and output predicted percentages.
       
Remaining:

- [ ] Export the predicted probabilities in an excel format.  
- [ ] Document variable definitions, temporal coverage, and known limitations.

### Phase 5 Reproduction 

**Status: Old files exist but need to be recreated in github repository. Lowest-priority new development task.**

Source: Section 3 of `ipums_acs_1year.csv` `ipums_acs_5year.csv`

Planned notebook: `notebooks/05_reproduction.Rmd`

Expected output: `reproduced_s15_model.xlsx` May also produce a an intermediate file saving the R models for future prediction 
       
Remaining:
- [ ] Load IPUMS csv data.
- [ ] Review the pervious series prediction generation files.
- [ ] Filter to San Diego County, and year = 2022 (confirm correct base year).
- [ ] create complex survey design.
- [ ] Reproduce the previous estimates while standardizing names and output formats for easy comparison to current models.
- [ ] Input predicted values for independent variables from series 15 and output predicted percentages.
- [ ] Input observed means for coefficients from the 2024 ACS data and output predictions using series 15 coefficients.
- [ ] Calculate counts from the 2024 ACS of each income catagory directly.
- [ ] Compare the count of households in each income category between the three distributions vis a Ridgeline Plot.
- [ ] Make three pairwise comparisons between the distributions using a chi-squared goodness of fit test.
- [ ] Repeat the steps above via a full model and PPO model.
- [ ] Compare the goodness of fit for all models and use that to inform final model selection.
- [ ] Document variable definitions and known limitations.

### Phase 6 - Model Comparisons and Selection of Final Model 

**Status: Not started, Fifth Highest-priority new development task.**

Source: Section 3 of `ipums_acs_1year.csv` `ipums_acs_5year.csv`

Planned notebook: `notebooks/06_model_selection.Rmd`

Expected output: `model_comparison.pdf`  

Remaining:
- [ ] Load IPUMS csv data.
- [ ] Filter to San Diego County.
- [ ] Create 2 dataframes one for base year (2022) and one for most recent year (2024). 
- [ ] create complex survey design.
- [ ] Run the full, bespoke, and Ologit models on the base year data following the procedures from each of those independent files.
- [ ] Input observed means for coefficients from the 2024 ACS data and output predictions for each of those models.
- [ ] Input the estimated coefficient values from the Series 15 predictions for 2024 into each model, calculating the predicted distributions.
- [ ] Calculate counts from the 2024 ACS of each income catagory directly.
- [ ] Compare the count of households in each income category between the three model predicted distributions, the ACS observed means, and the ACS observed counts, vis a Ridgeline Plot.
- [ ] Make three pairwise comparisons between the distributions using a chi-squared goodness of fit test for each model.
- [ ] Compare the goodness of fit for all models and use that to inform final model selection.
- [ ] Document results in a short 1–2-page report as a PDF, outlining the choice of preferred model.
- [ ] Document variable definitions and known limitations.

### Phase 7 Final predicted dataset

**Status: Not started, Second Lowest-priority new development task.**

Source: Section 3 of `ipums_acs_1year.csv` `ipums_acs_5year.csv`

Planned notebook: `notebooks/05_reproduction.Rmd`

Expected output: `s16_model.xlsx` (unclear about Estimates and Forecasts prefered output file type)
       
Remaining:
- [ ] Load IPUMS csv data.
- [ ] Filter to San Diego County, and year = 2024.
- [ ] create complex survey design.
- [ ] Reproduce clean code for the selected prediction model run on the 2024 data.
- [ ] Input matrix of predictions for relevant variables for all years covered by Series 16.
- [ ] Output the predicted count of households in each of the 14 income catagories
- [ ] Aggregate the count of households into the 5 income categories for Estimates and Forecasts 
- [ ] Document variable definitions and known limitations.

## 4. Standard Workflow for Each Processing Stage

Each new notebook should follow the same organization:

1. **Setup:** Load required libraries and shared configuration.
2. **Load inputs:** Verify files exist and required fields are present.
3. **Process data:** Apply clearly documented cleaning and transformation steps.
4. **Aggregate or join:** Define the expected observation unit and unique keys.
5. **Validate:** Check coverage, duplicates, missing values, ranges, and totals.
6. **Export:** Save the processed dataset to the configured output directory.
7. **Verify output:** Reopen the output and confirm its schema and record count.
8. **Document:** Record methodology, known issues, and validation status.

Avoid clearing the entire R environment or depending on objects created by previous notebooks. Each notebook should be runnable independently using its configured inputs.

## 5. Definition of Done

A processing stage is ready for review when:

- [ ] It runs successfully from a fresh R session with the required inputs available.
- [ ] No developer-specific file paths or credentials are hardcoded.
- [ ] Its input files and required packages are documented.
- [ ] The output file, fields, units, and unique keys are documented.
- [ ] Validation checks have been executed and reviewed.
- [ ] Historical-output comparison is completed where possible, or the reason it is pending is documented.
- [ ] Known methodological issues are identified.
- [ ] Generated datasets and local settings are excluded from Git.
- [ ] Changes are committed on a feature branch and submitted through a pull request.

A successful execution alone does not establish that a dataset is analytically validated.

## 6. Git and Code Review Practices

Use one feature branch per processing stage or focused improvement.

Examples:
- `feat/ipums_data_prep`
- `feat/model_comparison`
- `docs/data-dictionary`

Recommended workflow:

1. Create a GitHub issue describing the task and acceptance criteria.
2. Create a feature branch.
3. Implement and locally test the changes.
4. Commit logical units of work with descriptive messages.
5. Open a pull request to the appropriate integration branch.
6. Include a summary of input data, output data, validation results, and outstanding issues.
7. Merge after review.

Example commit messages:
- `feat: add 2023 data to forecast evaluation`
- `feat: harmonize reproduction variable names`
- `test: add houshold serial uniqueness checks`
- `docs: document IPUMS data and weighting assumptions`

Once the initial repository reorganization is merged, new feature branches should normally be created from `main`.

## 7. Reproducibility and Repository Maintenance

Additional improvements to implement as the pipeline matures:

- [ ] Create an input/output data inventory in `docs/`.
- [ ] Introduce `renv` to record tested R package versions.
- [ ] Create reusable functions for common operations and validation.
- [ ] Add automated tests for stable data contracts and small representative fixtures.
- [ ] Document the sequence and dependencies of processing stages.
- [ ] Establish a consistent convention for file names and output schemas.
- [ ] Review archived scripts for hardcoded credentials; revoke or replace exposed keys and ensure secrets are not included in new code.
- [ ] Remove or consolidate obsolete code only after the replacement has been validated.
- [ ] Document the process for rebuilding the complete estimation dataset.

## 8. Immediate Staff Priorities

The next developer should focus on the following, in order:

1. Review the working IPUMS notebook and complete cleaning.
2. Create the various R markdown files with the appropriate structure (even if blank). 
3. Document any issues that require methodological decisions before changing the underlying calculations.
4. Confirm desired output formats with Estimates and Forcastes
5. Pull forecasted mean HH characteristics 

The initial goal is a reproducible and reviewable data-preparation pipeline, not a redesign of the statistical estimation methodology.
