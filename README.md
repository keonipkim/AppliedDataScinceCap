# Falcon 9 First Stage Landing Prediction

Predict whether a Falcon 9 first stage lands successfully, starting from public launch records.

This is my completed [IBM Applied Data Science Capstone](https://www.coursera.org/learn/applied-data-science-capstone) on Coursera. The lab sequence, starter notebooks, and modeling datasets come from that course (IBM Skills Network, 2022).

## Question

SpaceX reuses the Falcon 9 first stage. This project asks the question that sits underneath reuse: given a launch, did the first stage land?

## Data

The wrangled table has 90 Falcon 9 launches, from 2010 on, across three sites:

| Launch site | Launches |
| --- | ---: |
| CCAFS SLC 40 | 55 |
| KSC LC 39A | 22 |
| VAFB SLC 4E | 13 |

Landing outcomes in that table include drone-ship landings (`True ASDS`, 41), ground-pad landings (`True RTLS`, 14), ocean landings (`True Ocean`, 5), failed attempts, and launches with no landing attempt.

The classifiers train on the course feature matrix: flight number and payload mass, plus one-hot columns for orbit, launch site, and related categories. The label is binary landing success. The split is 80/20 with `random_state=2`, which holds out 18 launches.

## Workflow

1. Scraped Falcon 9 and Falcon Heavy launch records from the Wikipedia page [List of Falcon 9 and Falcon Heavy launches](https://en.wikipedia.org/wiki/List_of_Falcon_9_and_Falcon_Heavy_launches).
2. Wrangled the records and counted launches by site, orbit, and landing outcome.
3. Queried the launches in SQLite.
4. Plotted payload, orbit, and launch site against landing success.
5. Mapped the launch sites with Folium.
6. Compared four classifiers with grid search and cross-validation: logistic regression, support vector machine, decision tree, and k-nearest neighbors.

## Results

| Model | Cross-validated accuracy | Holdout accuracy |
| --- | ---: | ---: |
| Logistic regression | 0.846 | 83.3% |
| Support vector machine | 0.863 | 83.3% |
| k-nearest neighbors | 0.848 | 83.3% |
| Decision tree | 0.873 | 83.3% |

Cross-validation is the comparison to trust. The holdout is 18 launches, and every model scored 83.3% on it.

The decision tree is the least stable of the four. That grid search scored 0.873 and warned that some parameter combinations had non-finite scores. Later runs in the same notebook landed nearby, not on the same number. Logistic regression (0.846), the support vector machine (0.863), and k-nearest neighbors (0.848) did not move.

## Notebooks

Read them in order. The saved outputs are from the course environment. Several cells call `piplite` and `fetch`, which belong to that in-browser runtime and will not run unchanged in a local kernel.

| Notebook | What it covers |
| --- | --- |
| [01_web_scraping.ipynb](notebooks/01_web_scraping.ipynb) | Wikipedia scrape with Requests and Beautiful Soup |
| [02_data_wrangling.ipynb](notebooks/02_data_wrangling.ipynb) | Missing values, launch sites, orbits, landing outcomes |
| [03_eda_sql.ipynb](notebooks/03_eda_sql.ipynb) | SQL queries against the launch table in SQLite |
| [04_eda_visualization.ipynb](notebooks/04_eda_visualization.ipynb) | Charts and feature preparation |
| [05_launch_site_map.ipynb](notebooks/05_launch_site_map.ipynb) | Interactive launch-site map with Folium |
| [06_landing_prediction.ipynb](notebooks/06_landing_prediction.ipynb) | Grid-search comparison of the four classifiers |

Slides from the capstone presentation: [falcon9_landing_prediction.pdf](docs/falcon9_landing_prediction.pdf).

## Stack

Python, pandas, NumPy, Requests, Beautiful Soup, SQLite, Matplotlib, Seaborn, Folium, and scikit-learn.

```bash
pip install -r requirements.txt
```

The SQL notebook was written in the course environment, which pinned `sqlalchemy==1.3.9` and `pandas==1.1.5`.
