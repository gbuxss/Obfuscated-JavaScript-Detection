# Machine Learning for Detecting Obfuscated JavaScript Malware

A supervised machine learning pipeline that detects obfuscated JavaScript malware through static code analysis. The system extracts 31 statistical and structural features from raw JavaScript source files and trains three classification models to distinguish between benign and obfuscated malicious code.

---

## Table of Contents

- [Overview](#overview)
- [Dataset](#dataset)
- [Project Structure](#project-structure)
- [Features](#features)
- [Models](#models)
- [Results](#results)
- [Getting Started](#getting-started)
- [Usage](#usage)

---

## Overview

JavaScript obfuscation is a common technique used by attackers to conceal malicious code from static analysis tools and security scanners. This project builds a machine learning pipeline that identifies obfuscated JavaScript files by analyzing their statistical and structural properties without executing the code.

The pipeline was developed and tested in Google Colaboratory using Python-based machine learning libraries.

---

## Dataset

The dataset consists of 3,375 labeled JavaScript files organized into two classes:

| Class | Label | Count | Percentage |
|-------|-------|-------|------------|
| Benign | 0 | 1,858 | 55.1% |
| Obfuscated | 1 | 1,517 | 44.9% |
| **Total** | | **3,375** | **100%** |

The dataset is split into 80% training (2,700 samples) and 20% testing (675 samples) using stratified sampling to preserve class proportions.

---

## Project Structure

```
javascript_malware/
├── data/
│   ├── JavascriptSamples/              # Benign JavaScript files
│   └── JavascriptSamplesObfuscated/    # Obfuscated JavaScript files
├── output/
│   ├── models/                         # Saved trained models (.pkl)
│   ├── figures/                        # Generated plots and visualizations
│   └── reports/                        # Classification reports and feature matrix
└── notebook.ipynb                      # Main Colab notebook
```

---

## Features

A total of 31 features are extracted from each JavaScript file across five categories:

### File Structure
| Feature | Description |
|---------|-------------|
| `file_size` | Total number of characters in the file |
| `num_lines` | Total number of lines |
| `avg_line_len` | Average number of characters per line |
| `max_line_len` | Length of the longest single line |

### Entropy
| Feature | Description |
|---------|-------------|
| `entropy` | Shannon entropy of the full file |
| `avg_identifier_entropy` | Mean entropy of all identifiers longer than 2 characters |

### Character Ratios
| Feature | Description |
|---------|-------------|
| `special_char_ratio` | Ratio of special characters to total characters |
| `digit_ratio` | Ratio of numeric characters to total characters |
| `uppercase_ratio` | Ratio of uppercase letters to total characters |
| `space_ratio` | Ratio of whitespace characters to total characters |

### Obfuscation Signals
| Feature | Description |
|---------|-------------|
| `hex_escape_count` | Count of `\x41` style hex escape sequences |
| `unicode_escape_count` | Count of `\u0041` style unicode escape sequences |
| `base64_count` | Count of base64-like strings longer than 40 characters |
| `long_string_count` | Count of string literals longer than 200 characters |
| `hex_array_index` | Count of hexadecimal array index patterns `[0x1f]` |
| `array_access_count` | Count of array access via hex index patterns |

### Dynamic Execution
| Feature | Description |
|---------|-------------|
| `eval_count` | Count of `eval()` calls |
| `unescape_count` | Count of `unescape()` calls |
| `atob_count` | Count of `atob()` calls |
| `function_constructor` | Count of `Function()` constructor calls |
| `settimeout_count` | Count of `setTimeout()` calls |
| `setinterval_count` | Count of `setInterval()` calls |
| `document_write` | Count of `document.write()` calls |

### Structural
| Feature | Description |
|---------|-------------|
| `num_identifiers` | Total count of identifiers in the file |
| `avg_identifier_len` | Average length of all identifiers |
| `num_functions` | Count of `function` keyword occurrences |
| `num_var_decl` | Count of `var` keyword occurrences |
| `num_semicolons` | Total count of semicolons |
| `num_commas` | Total count of commas |
| `bracket_ratio` | Ratio of parentheses to total characters |
| `square_bracket_ratio` | Ratio of square brackets to total characters |

---

## Models

Three classification models are trained and evaluated:

| Model | Description |
|-------|-------------|
| **Random Forest** | Ensemble of 200 decision trees with bootstrap sampling. No feature scaling required. |
| **Gradient Boosting** | Sequential ensemble of 200 trees with learning rate 0.1. Corrects residual errors iteratively. |
| **Logistic Regression** | Linear probabilistic classifier trained on StandardScaler-normalized features. |

---

## Results

All three models achieved strong performance on the 675-sample test set:

| Model | Accuracy | ROC AUC | False Negatives |
|-------|----------|---------|-----------------|
| Random Forest | **99.41%** | **99.92%** | **3** |
| Gradient Boosting | 99.26% | 99.90% | 4 |
| Logistic Regression | 98.22% | 98.71% | 9 |

**Random Forest** is the best performing model based on highest accuracy, highest ROC AUC, and fewest false negatives.

### Top Features by Importance

```
digit_ratio            ████████████████████  0.175
max_line_len           ███████████████████   0.155
avg_line_len           ███████████████████   0.153
space_ratio            ██████████████        0.120
num_lines              ███████████           0.090
num_var_decl           █████████             0.075
square_bracket_ratio   ████████              0.068
special_char_ratio     █████                 0.040
hex_escape_count       ████                  0.033
entropy                ███                   0.026
```

---

## Getting Started

### Prerequisites

```bash
pip install scikit-learn pandas numpy matplotlib seaborn
```

### Running in Google Colab

1. Upload the dataset zip file to Google Drive
2. Open `notebook.ipynb` in Google Colab
3. Mount Google Drive and update the `BASE_PATH` variable to match your folder location
4. Run all cells in order from top to bottom

### Dataset Setup

Organize your dataset in Google Drive as follows:

```
MyDrive/
└── javascript_malware/
    └── data/
        ├── JavascriptSamples/          # Place benign .js files here
        └── JavascriptSamplesObfuscated/ # Place obfuscated .js files here
```

---

## Usage

Update the base path at the top of the notebook to match your Drive location:

```python
BASE_PATH      = '/content/drive/MyDrive/javascript_malware'
BENIGN_DIR     = f'{BASE_PATH}/data/JavascriptSamples'
OBFUSCATED_DIR = f'{BASE_PATH}/data/JavascriptSamplesObfuscated'
```

The notebook will automatically create the following output directories:

```python
output/models/    # Saved model files (.pkl)
output/figures/   # EDA plots, confusion matrices, ROC curves, feature importance
output/reports/   # Feature matrix CSV and classification reports
```
