# Dataset Formatting Guide

KalEdge supports standard image datasets (such as MNIST and CIFAR-10) as well as custom multi-featured **CSV datasets**. This guide outlines how to structure and format your custom CSV datasets to ensure seamless compatibility with the data loader and training pipelines.

> **Note:** Support for uploading **custom image datasets** (e.g., zip files with categorized JPG/PNG structures) is currently under active development and will be enabled in an upcoming platform update.

---

## CSV File Structure

Your dataset must be uploaded as a single comma-separated values (`.csv`) file containing both your input features and target labels. The data loader automatically parses the headers and handles feature-label splitting.

### Requirements:

1.  **Tabular Format:** First row must contain the column headers (variable names).
2.  **No Missing Values:** Clean or impute any `NaN`, `null`, or missing numeric cells prior to upload.
3.  **Target Column:** The target column containing your ground-truth labels must be present, numeric, and categorical (for classification tasks).

---

## Classification Dataset Example

If you are training a classifier (e.g., classifying a 16-channel physical sensor reading into 3 distinct target classes/states), your CSV should look like this:

| ch0 | ch1 | ch2 | ... | ch15 | target |
|:---:|:---:|:---:|:---:|:----:|:------:|
| 12.4| 45.1|  2.0| ... | 102.1|    0   |
| 15.1| 38.0|  1.5| ... |  98.4|    1   |
|  9.8| 41.2|  0.8| ... | 115.0|    0   |
| 22.0| 50.4|  3.1| ... |  88.2|    2   |

### Rules for target/labels:
*   The target column **must contain integers** starting from `0` to `N-1`, where `N` is the total number of classes.
*   For example, for 3 classes, the targets must be strictly `0`, `1`, or `2`.
*   Avoid string labels like `"class_A"`, `"class_B"`, or `"anomaly"`. Use numerical mapping instead. You can define class names within the KalEdge dashboard when configuring training parameters.

---

## Upload and Loading Process

1.  **Select Custom CSV** in the **Dataset** tab of the dashboard.
2.  **Upload File:** Drag and drop your `.csv` file.
3.  **Configure Target Column:** 
    *   While KalEdge attempts to auto-detect common labels, you must type or select the **exact header name** of your target column (e.g., `target` or `class`) in the input box.
    *   KalEdge uses this to isolate the ground-truth labels from your input features.
4.  **Scaling and Normalization (Norm Mode):**
    *   FPGAs perform best with inputs restricted to the `[-1, 1]` range because it limits the bitwidths and integer overhead required in fixed-point operations.
    *   **Per-Feature Scaling:** Rescales each feature/column independently to `[-1, 1]` using scikit-learn's `MinMaxScaler`. This is highly recommended for tabular datasets with mixed physical ranges.
    *   **Per-Sample Scaling:** Rescales each sample/row independently to `[-1, 1]` based on its minimum and maximum values. This is ideal for time series, 1D signals, or spectral sweeps where you want to preserve the relative shape of each individual signal.
    *   **None (Raw):** Keeps the original CSV numeric values untouched.
5.  **Train/Test Split:** Specify the train-validation-test split percentages (e.g., 70% Train, 15% Val, 15% Test).
6.  **Load:** Click **Load Dataset** to complete the stage.

---

## Preprocessing and the AI Dataset Agent (Upcoming)

If you are unsure of the optimal preprocessing strategy:
*   The conversational **AI Dataset Agent** (currently under active development for Pro subscription accounts) is designed to help you analyze your feature distributions, check for imbalances, and recommend the best scaling or data-cleaning steps.
*   Once released, clicking **Analyze Dataset** will let the agent scan your statistical metrics, suggest optimal class weights to counter training bias, and output an interactive visual diagnostic report.
