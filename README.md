# Inflation Sentiment & Rates Expectations NLP Pipeline

A quantitative macro research project developed for the **Citadel Central Bank Challenge**, where our team finished **2nd place**. The project combines natural language processing, macroeconomic analysis, and financial market interpretation to generate a forward-looking inflation and rates sentiment signal from news data.

The core objective was to support a monetary policy recommendation by extracting market-relevant sentiment from financial news and central bank commentary, then aggregating it into interpretable daily macro signals.

---

## 🏆 Competition Result

| Field | Detail |
|---|---|
| Competition | Citadel Central Bank Challenge |
| Placement | **2nd Place** |
| Prize | Narrowly missed the £15,000 team prize |

Our team's final recommendation was to **hold interest rates**, while highlighting that **future hikes may be necessary if inflation expectations show signs of de-anchoring**.

---

## Motivation

Traditional macroeconomic indicators (CPI, wage growth, services inflation, central bank communications) are released with delays and often require interpretation. Financial news provides a high-frequency source of information about:

- Inflation expectations
- Interest rate expectations
- Monetary policy sentiment
- Market reaction to macroeconomic releases
- Forward-looking commentary from analysts and policymakers

The goal of this project was to create a systematic way of extracting that information from text and converting it into a usable macro signal.

---

## Key Features

- Automated financial news collection
- Trusted-domain filtering for source reliability
- Sentence-level macro relevance filtering
- Forward-looking keyword detection
- Transformer-based sentiment classification
- Directional inflation sentiment scoring
- Entropy-based confidence weighting
- Article-level and daily aggregation
- Rolling averages and smoothing
- Data visualisation for policy presentation
- Interpretation of NLP outputs in a macroeconomic framework

---

## Methodology

### 1. Data Collection

The pipeline collects financial news articles from selected trusted sources using news APIs. Sources are restricted to high-quality macroeconomic, financial, and institutional domains to reduce noise and improve signal reliability.

Source categories include: central banks, financial newspapers, market data providers, and reputable economic journalism outlets.

---

### 2. Article Filtering

Raw articles are filtered using macro-relevant keywords such as:

`inflation` · `CPI` · `core inflation` · `interest rates` · `rate hikes` · `rate cuts` · `monetary policy` · `Bank of England` · `Federal Reserve` · `ECB` · `services inflation` · `wage growth` · `sticky inflation` · `disinflation`

---

### 3. Sentence Extraction

Rather than classifying entire articles, the pipeline extracts individual sentences most likely to contain forward-looking macro information. This improves signal quality by isolating directionally relevant text from historical context or political commentary.

Forward-looking terms prioritised:

`expected` · `forecast` · `likely` · `projected` · `outlook` · `anticipate` · `may rise` · `could fall` · `risk of` · `pricing in`

---

### 4. Sentiment Classification

Each selected sentence is passed through a transformer-based classifier predicting whether inflation or rate expectations are likely to move **Up**, **Neutral**, or **Down**.

The core directional score is:

```
score = P(up) - P(down)
```

This creates a continuous sentiment score rather than relying on hard class labels.

---

### 5. Confidence Weighting

Entropy-based confidence weighting is applied so that uncertain predictions contribute less to the final signal.

**Entropy:**
```
H = -Σ p_i · log(p_i)
```

**Normalised entropy:**
```
H_norm = H / log(3)
```

**Sentence confidence weight:**
```
w = |P(up) - P(down)| × (1 - H_norm)
```

This rewards predictions that are both directionally strong and low-entropy.

---

### 6. Article-Level Aggregation

Each article receives a weighted sentiment score from its extracted sentences:

```
article_score = Σ(w_i × s_i) / Σ(w_i)
```

---

### 7. Daily Aggregation

Article scores are aggregated by date to produce a daily macro sentiment time series using simple averages, confidence-weighted averages, rolling averages, or exponential smoothing.

**Signal interpretation:**

| Signal | Interpretation |
|---|---|
| Positive | Upward pressure on inflation / rates expectations |
| Near-zero | Neutral or mixed expectations |
| Negative | Downward pressure on inflation / rates expectations |

---

## Project Structure

```
.
├── data/
│   ├── raw/
│   │   └── raw_articles.csv
│   ├── processed/
│   │   └── daily_news_sentiment_results.csv
│   └── external/
│       └── macro_event_dates.csv
│
├── notebooks/
│   ├── 01_data_collection.ipynb
│   ├── 02_sentence_filtering.ipynb
│   ├── 03_sentiment_classification.ipynb
│   ├── 04_signal_aggregation.ipynb
│   └── 05_visualisation_and_analysis.ipynb
│
├── src/
│   ├── data_collection.py
│   ├── article_filtering.py
│   ├── sentence_extraction.py
│   ├── sentiment_model.py
│   ├── aggregation.py
│   ├── entropy.py
│   ├── plotting.py
│   └── utils.py
│
├── outputs/
│   ├── figures/
│   └── results/
│
├── requirements.txt
├── README.md
└── .gitignore
```

---

## Core Components

| Module | Responsibilities |
|---|---|
| `data_collection.py` | Query construction, API requests, date-based collection, source filtering |
| `article_filtering.py` | Removing irrelevant articles, trusted domain prioritisation, topic filtering |
| `sentence_extraction.py` | Sentence tokenisation, forward-looking detection, relevance scoring |
| `sentiment_model.py` | Transformer / SetFit inference, class probabilities, directional scores |
| `entropy.py` | Prediction entropy, normalised entropy, confidence weighting |
| `aggregation.py` | Sentence → article → daily signal aggregation, rolling averages |
| `plotting.py` | Daily sentiment charts, rolling averages, CPI / BoE event overlays |

---

## Signal Formulae

**Sentence score:**
```
s_i = P_i(up) - P_i(down)
```

**Sentence confidence weight:**
```
w_i = |P_i(up) - P_i(down)| × (1 - H_i / log(3))
```

**Article sentiment score:**
```
S_article = Σ(w_i · s_i) / Σ(w_i)
```

**Daily sentiment score:**
```
S_day = mean(S_article)
```

**Confidence-weighted daily score:**
```
S_day_weighted = Σ(W_j · S_j) / Σ(W_j)
```

*Where W_j is the confidence weight for article j.*

---

## Visual Outputs

- Daily inflation sentiment score
- Confidence-weighted daily sentiment score
- Rolling sentiment averages
- Article count by day
- Inter-article disagreement / entropy
- Sentiment overlaid with CPI release dates
- Sentiment overlaid with Bank of England meeting dates

---

## Installation

```bash
# Clone the repository
git clone https://github.com/your-username/inflation-sentiment-nlp.git
cd inflation-sentiment-nlp

# Create and activate a virtual environment
python -m venv venv
source venv/bin/activate        # macOS/Linux
venv\Scripts\activate           # Windows

# Install dependencies
pip install -r requirements.txt
```

---

## Example Usage

**Collect articles:**
```python
from src.data_collection import build_daily_news_results

results = build_daily_news_results(
    api_key="YOUR_API_KEY",
    query=query,
    start_date="2026-03-01",
    end_date="2026-04-01",
    domains=trusted_domains
)
```

**Run sentiment classification:**
```python
from src.sentiment_model import rates_expectations

output = rates_expectations(
    model=model,
    texts=selected_sentences,
    labels=("down", "neutral", "up")
)
```

**Aggregate daily scores:**
```python
from src.aggregation import aggregate_daily_sentiment

daily_results = aggregate_daily_sentiment(article_scores)
```

**Plot the final signal:**
```python
from src.plotting import plot_daily_sentiment

plot_daily_sentiment(daily_results)
```

---

## Technologies Used

![Python](https://img.shields.io/badge/Python-3776AB?style=flat&logo=python&logoColor=white)
![Pandas](https://img.shields.io/badge/Pandas-150458?style=flat&logo=pandas&logoColor=white)
![NumPy](https://img.shields.io/badge/NumPy-013243?style=flat&logo=numpy&logoColor=white)
![HuggingFace](https://img.shields.io/badge/HuggingFace-FFD21E?style=flat&logo=huggingface&logoColor=black)
![scikit-learn](https://img.shields.io/badge/scikit--learn-F7931E?style=flat&logo=scikit-learn&logoColor=white)
![Jupyter](https://img.shields.io/badge/Jupyter-F37626?style=flat&logo=jupyter&logoColor=white)

`pandas` · `NumPy` · `matplotlib` · `scikit-learn` · `Hugging Face Transformers` · `SetFit` · `sentence-transformers` · `NewsAPI / GNews` · `trafilatura`

---

## Limitations

- News coverage may be biased toward major economies
- Global financial news may not perfectly represent UK-specific inflation expectations
- API limits can restrict article coverage
- Text classifiers can misread sarcasm, hedging, or complex policy language
- Short-term news sentiment may be noisy
- Sentiment does not directly imply causation

The pipeline should be interpreted as a **supporting quantitative signal**, not a standalone decision engine.

---

## Future Improvements

- [ ] Add Twitter / X data for higher-frequency market sentiment
- [ ] Incorporate central bank speeches directly
- [ ] Compare signal against market-implied rates curves
- [ ] Test correlation with gilt yields and SONIA futures
- [ ] Build a UK-specific article source filter
- [ ] Fine-tune a domain-specific macro-finance language model
- [ ] Add topic modelling to separate inflation, growth, labour, and policy themes
- [ ] Build a real-time monitoring dashboard
- [ ] Backtest signal behaviour around historical CPI and BoE events

---

## Key Takeaways

This project demonstrated that NLP can convert unstructured financial news into a structured macroeconomic signal. The most important design choice was carefully controlling the full pipeline:

> **source quality → sentence relevance → forward-looking filtering → probabilistic classification → confidence weighting → macro interpretation**

The result is a robust, interpretable signal capable of supporting policy reasoning.

---

## Authors

Developed as part of a team submission for the **Citadel Central Bank Challenge** · 2nd Place finish.

---

> **Disclaimer:** This project was developed for educational and competition purposes. It is not financial advice, monetary policy advice, or a production trading system.