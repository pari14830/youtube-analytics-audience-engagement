# 📺 YouTube Analytics & Audience Engagement in Germany

**Ironhack Data Analytics, Module 1: Data Wrangling and Retrieval Project**
**Team Nairobi:** Pariya & Zahid

---

## 📌 Introduction

Views show how many people watched a video. Likes and comments show how many people **reacted** to it.

In this project, we analyze trending YouTube videos in Germany to find out which video characteristics lead to more engagement. We compare trending videos from **2020–2024** (Kaggle) with trending videos from **today** (YouTube Data API, 2026).

**Main question:** *What drives engagement on trending YouTube videos in Germany?*

We measure engagement as a share of views:

- **Like-rate** = likes ÷ views × 100
- **Comment-rate** = comments ÷ views × 100

---

## 📦 Data Sources

| # | Source | Type | Period | Size | What it gives us |
|---|---|---|---|---|---|
| 1 | [YouTube Data API v3](https://developers.google.com/youtube/v3) (`videos` endpoint, `chart=mostPopular`, `regionCode=DE`) | API | One-day snapshot, 2026 | 200 videos | Current trending videos, incl. **video duration** |
| 2 | [YouTube Trending Video Dataset (Kaggle)](https://www.kaggle.com/datasets/rsrishav/youtube-trending-video-dataset) (`DE_youtube_trending_data.csv` + `DE_category_id.json`) | CSV + JSON | Aug 2020 – Apr 2024 | ≈55,000 videos | Historical trending videos + category names |

> ⚠️ The raw Kaggle file (≈450 MB) is **not** in this repo because GitHub does not accept files over 100 MB. Download it from the Kaggle link above and put it in the project folder to run `02_kaggle_data.ipynb`.

> 🔒 The API key is entered with `getpass`, so it is never saved in the notebooks.

---

## ❓ Research Questions & Hypotheses

| # | Question | Hypothesis | Data used |
|---|---|---|---|
| 1 | Does **video length** affect engagement? | Shorter videos (incl. Shorts) get more engagement | API (Kaggle has no duration) |
| 2 | Which **category** gets the most engagement? | Music has the highest engagement | API + Kaggle |
| 3 | Does **publish time** matter? | Videos published on weekends get more views | Kaggle |
| 4 | Do **clickbait titles** get more views? | Titles with `!`, `?` or CAPS words get more views | Kaggle |

---

## 🛠️ Methodology

### 1. Data collection
- **API:** 4 requests with pagination (`nextPageToken`) to get all 200 trending videos in Germany. Saved once as a raw CSV, so the results don't change when the notebook runs again.
- **Kaggle:** downloaded the German trending dataset and the category JSON file.

### 2. Data cleaning (both sources into the same 15 columns)
- **Renamed columns** to one shared format (e.g. `channelTitle` → `channel`)
- **Converted dates** to datetime and from **UTC to Berlin time** before extracting weekday and hour
- **Converted duration** from ISO 8601 text (`PT2M43S`) to seconds
- **Missing values:** hidden likes/comments stay **NaN**, not 0 (0 would be wrong data)
- **Removed duplicates:** one row per Kaggle video
- **Dropped 0-view rows** (live streams), because rates can't be calculated
- **New columns:** `publish_weekday`, `publish_hour`, `like_rate`, `comment_rate`, `period`
- **Merged** category names from the JSON file

### 3. Combining & EDA
- Combined both clean files with `concat`
- Compared groups with **medians** (the data is right-skewed: a few viral videos have huge view counts)
- Visualized results with `matplotlib` and `seaborn`

---

## 📊 Findings

| # | Hypothesis | Result | Key numbers |
|---|---|---|---|
| 1 | Shorter videos get more engagement | ❌ Not supported | Shorts 1.74% vs. long videos 2.68% median like-rate; 20–60 min videos highest (2.79%) |
| 2 | Music has the highest engagement | ❌ Not supported | 2020–2024: Comedy 6.91% > Music 6.76% ≈ Gaming 6.73% |
| 3 | Weekend videos get more views | ❌ Not supported | Weekend ≈1.77M vs. weekdays ≈1.89M average views; Sunday lowest |
| 4 | Clickbait titles get more views | ❌ Not supported (the opposite) | Median views 293,753 (clickbait) vs. 520,882 (normal titles): **44% fewer** |

**Key takeaways:**
- **Longer videos** (20–60 min) engage viewers more than short ones.
- **Views ≠ engagement:** the categories with the most views are not the ones with the highest like/comment rates.
- German trending has shifted strongly towards **Gaming**: ≈77% of trending videos today vs. ≈12% in 2020–2024.
- **Publish time** matters less than expected.
- **Clickbait titles** get fewer views, not more.

---

## ⚠️ Limitations

- Trending videos are only the **most successful** videos, not all of YouTube.
- The API data is a **one-day snapshot** (200 videos); Kaggle covers **3.5 years** (≈55k videos).
- Kaggle has **no duration**, so Question 1 uses only the 200 API videos; some length groups are very small.
- "Short" is defined by length (≤ 3 min), not by YouTube's own label.
- Names written in capitals (e.g. "FIFA") are also counted as CAPS words in Question 4.

---

## 🚀 Next Steps

- Collect API data over **several weeks** for a fairer comparison
- Investigate **why Gaming** dominates trending today
- Fetch **duration for Kaggle videos** via the API (using `video_id`)
- Test whether the differences are **statistically significant**

---

## 📁 Repository Structure

```
├── 01_api_data.ipynb                   # API data: collection + cleaning (Pariya)
├── 02_kaggle_data.ipynb                # Kaggle data: cleaning (Zahid)
├── 03_combine_eda.ipynb                # Combine both + EDA + conclusions
├── youtube_trending_de_api_raw.csv     # Raw API snapshot (200 videos)
├── api_clean.csv                       # Clean API data
├── kaggle_clean.csv                    # Clean Kaggle data
├── DE_category_id.json                 # Category names (Kaggle)
├── .gitignore                          # Excludes the large raw Kaggle file
└── README.md
```

**How to run:** run the notebooks in order (01 → 02 → 03). All files are read from the same folder as the notebooks.

---

## 🔗 Links

- 📊 [Presentation (Google Slides)](https://docs.google.com/presentation/d/1hOtDxoRKDKNQl8nruKw_bY1F3n4E4Q49/edit?usp=sharing)
- 📋 [Trello board](https://trello.com/b/EJa24f1T/my-trello-board)
- 📦 [Kaggle dataset](https://www.kaggle.com/datasets/rsrishav/youtube-trending-video-dataset)
- 🔌 [YouTube Data API v3 documentation](https://developers.google.com/youtube/v3)
