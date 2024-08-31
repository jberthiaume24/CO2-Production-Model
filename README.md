# Performance Metrics of Chemical Processing: A Regression Analysis of Belle Ayr Data

## Overview

This README provides a detailed guide on setting up the environment for analyzing performance metrics of chemical processing using R Markdown and RStudio. It also summarizes key findings from the regression analysis of the Belle Ayr data.

## Cloning the Repository

To get started with this project, you need to clone the GitHub repository. You can do this using the following command:


```bash
git clone https://github.com/jberthiaume24/CO2-Production-Model.git
```

After cloning the repository, navigate to the project directory:

```bash
cd CO2-Production-Model
```

## Dataset Information

The dataset used in this project can be found here: [Dataset Information](https://rdrr.io/github/shafayetShafee/datapackage/man/DATASET.html). This dataset is crucial for the analysis and provides the foundation for our results.

## Setup Instructions

### Prerequisites

- **R**: Ensure you have R installed. You can download it from the [CRAN website](https://cran.r-project.org/).
- **RStudio**: Install RStudio, an integrated development environment (IDE) for R. Download it from the [RStudio website](https://rstudio.com/products/rstudio/download/).

### Installing R Packages

To ensure that all necessary R packages are available, follow these steps:

1. Open RStudio.
2. Install the required packages by running the following commands in the R console:

    ```r
    install.packages(c("rmarkdown", "ggplot2", "dplyr", "tidyr", "knitr"))
    ```

    Here’s a brief description of each package:
    
    - `rmarkdown`: Enables the creation of dynamic documents in R.
    - `ggplot2`: Provides a powerful framework for creating plots and visualizations.
    - `dplyr`: Facilitates data manipulation with a consistent set of verbs.
    - `tidyr`: Helps in tidying and reshaping data.
    - `knitr`: Used for dynamic report generation.

### Setting Up R Markdown

1. **Create a New R Markdown File**:
    - In RStudio, go to `File` -> `New File` -> `R Markdown`.
    - Provide a title and author name, then click `OK`.

2. **Configure YAML Header**:
    The YAML header of your R Markdown document should look like this:

    ```markdown
    ---
    title: "Performance Metrics of Chemical Processing: A Regression Analysis of Belle Ayr Data"
    author: "Joshua Berthiaume"
    date: "`r Sys.Date()`"
    output: html_document
    ---
    ```

3. **Knit the Document**:
    - Click the `Knit` button in RStudio to generate the HTML, PDF, or Word document from your R Markdown file.

## Analysis and Findings

### Major Steps and Conclusions

1. **Loading the Data**

    We loaded the dataset from a CSV file:

    ```r
    dat <- read.csv("data.csv")
    ```

2. **Building the Multiple Regression Model**

    A multiple regression model was built to relate CO2 (y) to total solvent (x6) and hydrogen consumption (x7):

    ```r
    model <- lm(y ~ x6 + x7, data = dat)
    summary(model)
    ```

    - **Equation of the Fitted Line**:
      \[
      \hat{Y} = 2.526 + 0.019(x6) + 2.186(x7)
      \]

3. **Testing the Significance of the Regression Model**

    We performed an ANOVA to test the overall significance of the regression model:

    ```r
    anova(model)
    ```

    - **F-Test for Overall Significance**:
        - **F-Statistic**: 27.95
        - **Critical Value**: 3.40
        - **p-value**: Calculated as \(1 - \text{pf}(27.95, 2, 24)\), which is less than 0.05.

    - **Interpretation**:
        - The F-statistic is much greater than the critical value, leading to the rejection of the null hypothesis. This indicates that at least one of the predictors (x6 or x7) is significant.
        - **Model Fit**:
            - \( R^2 = 0.6996 \)
            - **Adjusted \( R^2 \)**: 0.6746

4. **Testing the Contribution of Individual Predictors**

    We tested the significance of each predictor using t-tests:

    ```r
    n <- nrow(dat)
    k <- 2 + 1
    t_critical <- qt(1 - 0.05 / 2, n - k)
    print(t_critical)
    ```

    - **For β₆ (Total Solvent)**:
        - **Test Statistic**: 6.74
        - **Conclusion**: Since 6.74 > ±2.064, we reject the null hypothesis. Total solvent (x6) is significantly related to CO2.

    - **For β₇ (Hydrogen Consumption)**:
        - **Test Statistic**: 2.247
        - **Conclusion**: Since 2.247 > ±2.064, we reject the null hypothesis. Hydrogen consumption (x7) is significantly related to CO2.

5. **Confidence Intervals**

    - **95% Confidence Interval for β₆**:
      ```r
      confint(model, "x6")
      ```
      - **Conclusion**: The 95% confidence interval for β₆ is (0.0129, 0.0242).

    - **90% Confidence Interval for Mean CO2**:
      ```r
      predict(model, interval="confidence", newdata = data.frame(x6=3200, x7=7.2), level=.9)
      ```
      - **Conclusion**: The 90% confidence interval for the mean CO2 when x6 = 3200 and x7 = 7.2 is (64.50, 90.57).

    - **90% Prediction Interval for Actual CO2**:
      ```r
      predict(model, interval="prediction", newdata = data.frame(x6=3200, x7=7.2), level=.9)
      ```
      - **Conclusion**: The 90% prediction interval for actual CO2 is (56.13, 98.94).

6. **Refitting the Model Using Only x6**

    We refitted the model using only x6 as the regressor:

    ```r
    model.2 <- lm(y ~ x6, data = dat)
    summary(model.2)
    anova(model.2)
    ```

    - **Testing the Significance of the Reduced Model**:
      ```r
      1 - pf(43.766, 1, 25)
      alpha = 0.05
      qf(1 - alpha, 1, 25)
      ```
      - **F-Test Statistic**: 9.74
      - **Critical Value**: 4.24
      - **Conclusion**: Since 9.74 > 4.24, we reject the null hypothesis. The model with x6 alone is effective in explaining the relationship with CO2.

7. **Comparing Models**

    - **95% Confidence Intervals for β₆**:
      ```r
      confint(model.2, "x6")
      confint(model, "x6")
      ```

      - **Conclusion**:
        - For Model 2 (x6 only), the 95% CI for β₆ is (0.0134, 0.0254).
        - For Model 1 (x6 and x7), the 95% CI for β₆ is (0.0129, 0.0242).
        - The confidence intervals are similar, suggesting that the contribution of x7 is not as significant compared to x6 alone.

## Conclusion

The analysis shows that both total solvent and hydrogen consumption are significant predictors of CO2 emissions. However, when refitting the model using only total solvent (x6), the results indicate that x6 alone provides a sufficiently good fit, suggesting that x7 does not add significant predictive power. 

For further insights and details, refer to the generated report or consult the [R Markdown Documentation](https://rmarkdown.rstudio.com/) and other related resources.

## License

This project is licensed under the [MIT License](LICENSE).
