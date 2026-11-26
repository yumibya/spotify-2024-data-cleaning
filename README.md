# Data Cleaning Project — Most Streamed Spotify Songs 2024

A data cleaning project in Python (pandas) that turns a messy, cross-platform music dataset into an analysis-ready file.

## Project Goal

This project focuses on **data cleaning**: assessing data quality, fixing data types, handling missing values and reviewing outliers, with every decision documented and justified.

Cleaning decisions were guided by one analytical question the cleaned data is designed to support:

> **Is TikTok activity (posts, likes, views) related to a song's Spotify streams?**

For example, columns needed for this question were kept even when partly missing, while columns irrelevant to it were dropped.

## Dataset

- **Source:** [Most Streamed Spotify Songs 2024](https://www.kaggle.com/datasets/nelgiriyewithana/most-streamed-spotify-songs-2024) by Nidula Elgiriyewithana, Kaggle
- **Size:** 4,600 songs × 29 columns
- **Coverage:** songs released between 1987 and mid-2024, with cross-platform metrics from Spotify, YouTube, TikTok, Apple Music, Deezer, Amazon, Pandora, Shazam and radio (AirPlay, SiriusXM)
- **Encoding:** the raw file must be read with `encoding="latin-1"`

## Repository Structure

| File | Description |
|---|---|
| `Spotify_Analysis.ipynb` | Jupyter notebook with all cleaning steps and explanations |
| `Most Streamed Spotify Songs 2024.csv` | Raw dataset from Kaggle |
| `spotify_clean.csv` | Cleaned dataset (4,485 rows × 28 columns) |

## Data Cleaning Process

### 1. Data quality check
- Measured missing values per column. Missingness ranged from 100% (TIDAL Popularity) to under 3% for core Spotify columns.
- Found and removed **2 duplicate rows**.

### 2. Data types
- Converted 17 count columns stored as text with comma separators (for example, `"390,470,936"`) into numbers.
- Verified that **no values were lost** in the conversion by comparing missing counts before and after.
- Converted `Release Date` to a datetime and derived a `release_year` column.

### 3. Missing values
Each column was treated according to how much data was missing and how important it is to the research question:

| Situation | Treatment | Columns |
|---|---|---|
| Mostly empty or not relevant | Dropped | TIDAL Popularity (100% missing), Soundcloud Streams (72.5%), SiriusXM Spins (not relevant) |
| Outcome variable | Deleted rows with missing values (113 rows) | Spotify Streams |
| Text, cannot be recovered | Filled with `"Unknown"` | Artist (5 rows) |
| Few missing, numeric | Imputed with the median | Spotify Playlist Count, Spotify Playlist Reach |
| Many missing, key variable | Kept as missing, plus a flag column | TikTok Views, Likes and Posts → `has_tiktok_data` |

**Why keep TikTok values as missing?** About one in five songs has no TikTok data. Filling hundreds of rows with a single value would hide real differences between songs and weaken the relationship being measured. The `has_tiktok_data` flag lets later analysis compare songs with and without TikTok activity.

### 4. Outliers
- Calculated IQR limits and outlier counts for every numeric column, and plotted boxplots.
- Checked the top three values in each column. The extreme values are genuine global hits (for example, *Blinding Lights* on Spotify and *Baby Shark* on YouTube), not errors.
- **Decision: outliers were kept**, because they are the most important songs for the TikTok–Spotify question.
- Most numeric columns are strongly right-skewed, which is expected for popularity data, where a few hits dominate.

## Result

| | Raw | Clean |
|---|---|---|
| Rows | 4,600 | 4,485 |
| Columns | 29 | 28 |
| Songs with TikTok data | — | 80.2% |

## Tools

- Python 3
- pandas, NumPy
- Matplotlib
- Jupyter Notebook

## How to Run

```bash
pip install pandas numpy matplotlib jupyter
jupyter notebook Spotify_Analysis.ipynb
```

Place `Most Streamed Spotify Songs 2024.csv` in the same folder as the notebook before running.

## Key Takeaways

- **No data was lost silently:** each conversion was checked by comparing missing counts before and after.
- **Missing values were treated column by column**, not with one blanket rule, based on how much was missing and how important the column is.
- **Outliers were investigated, not automatically removed:** extreme values were confirmed as genuine hits.
- **The cleaned file is ready for analysis**, such as correlating TikTok metrics with Spotify streams (Spearman correlation and log scales are recommended, given the strong skew).

## Data Source and Acknowledgement

Elgiriyewithana, N. (2024). *Most Streamed Spotify Songs 2024* [Data set]. Kaggle. https://www.kaggle.com/datasets/nelgiriyewithana/most-streamed-spotify-songs-2024
