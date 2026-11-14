# Dry Bean Classification and Clustering

This project compares several machine learning models that predict the variety of a dry bean from 16 shape measurements. It explores whether the measurements form natural groups when the variety labels are hidden. It was written for a classroom competency. It is not ready to control real sorting equipment without additional testing.

## Dataset

Koklu, M., and Ozkan, I. A. (2020). Dry Bean [Dataset]. UCI Machine Learning Repository. https://doi.org/10.24432/C50S4B. The dataset is licensed under CC BY 4.0.

It has 13,611 examples, 16 numeric measurements derived from bean images and a Class target with seven bean varieties.

## Files

- `DryBeans_SergioAranda.ipynb`: the notebook with all code, results and written notes
- `requirements.txt`: the package versions used

## Setup

1. Install Python 3.13.
2. Install the packages: `python -m pip install -r requirements.txt`
3. Open `DryBeans_SergioAranda.ipynb` in Jupyter or VS Code and choose Run All. The notebook downloads the dataset from the UCI repository with the `ucimlrepo` package, so it needs an internet connection.

## Method

- The data was split into 80% training and 20% test with a stratified split (`random_state=42`).
- The four models were compared with 5-fold stratified cross-validation on the training data (`shuffle=True`, `random_state=42`), with the same folds for every model.
- Logistic regression used StandardScaler and `max_iter=1000`. The decision tree, random forest and histogram gradient boosting model used their default settings with `random_state=42`.
- The test set was used once, for the chosen model only.
- For clustering, k-means (`n_init=10`, `random_state=42`) was run on the scaled measurements of all 13,611 beans without the labels. Silhouette scores were computed on a random sample of 5,000 beans.

## Results

Cross-validation results on the training data of 5 folds:

| Model | Train accuracy | Validation accuracy | Gap | Fit time (s) | Score time (s) |
|-------|----------------|---------------------|-----|--------------|----------------|
| Logistic regression | 0.926 | 0.9239 | 0.0021 | 0.3318 | 0.0072 |
| Decision tree | 1.000 | 0.8954 | 0.1046 | 0.4309 | 0.0076 |
| Random forest | 1.000 | 0.9237 | 0.0763 | 6.5349 | 0.0460 |
| Hist gradient boosting | 1.000 | 0.9280 | 0.0720 | 5.1599 | 0.0905 |

Score time is for one validation fold of about 2,178 beans. The decision tree, random forest and boosting model overfit the training data. Logistic regression did not.

I recommend logistic regression with StandardScaler. Its test accuracy was 0.9207, close to its validation accuracy of 0.9239. It's fast, simple to maintain and has almost no overfitting. Boosting scored about 0.004 higher but trained and scored more slowly and is harder to explain. I did not check which bean varieties the model confuses. I did not measure how much the scores vary between folds either.

Clustering: k = 3 had the highest silhouette score of 0.402 and all scores were below 0.5. One cluster held 522 of the 522 Bombay beans and the other two clusters mixed several varieties. A cluster is not the same as a bean variety.

## Reproducibility

- Random seed: 42 for the split, the cross-validation folds, the models and k-means.
- Python 3.13.5, numpy 2.1.3, pandas 2.2.3, scikit-learn 1.6.1, ucimlrepo 0.0.7.
- Timings depend on the computer and will differ between runs.