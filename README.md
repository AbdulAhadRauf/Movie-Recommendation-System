
# Movie Recommender System

This project is a content-based movie recommender system that suggests movies similar to a user's choice. The recommendation is based on movie metadata like genres, keywords, cast, and crew. The core of this system is to create a "tag" for each movie by combining these features and then finding the similarity between movies using these tags.

## 📝 Project Overview

The goal of this project is to build a recommendation engine that can suggest a list of 5 movies based on a single movie input. The system does not rely on user ratings (collaborative filtering) but rather on the content and attributes of the movies themselves (content-based filtering). By vectorizing the key attributes of movies, we can calculate a similarity score between them and recommend the ones that are most alike.

## 💾 Dataset

This project uses the [TMDB 5000 Movie Metadata](https://www.kaggle.com/datasets/tmdb/tmdb-movie-metadata) dataset from Kaggle. It consists of two CSV files:

1.  `tmdb_5000_movies.csv`: Contains detailed information about 5000 movies, including budget, genres, keywords, overview, etc.
2.  `tmdb_5000_credits.csv`: Contains information about the cast and crew for each movie.

## 🛠️ Methodology

The recommendation system is built through a series of data processing and machine learning steps.

### 1\. Data Loading and Merging

  - The `tmdb_5000_movies.csv` and `tmdb_5000_credits.csv` datasets are loaded into pandas DataFrames.
  - The two DataFrames are merged into a single DataFrame based on the movie ID.

### 2\. Data Cleaning and Preprocessing

  - From the merged dataset, only the essential columns are selected: `id`, `original_title`, `genres`, `keywords`, `overview`, `cast`, and `crew`.
  - Rows with missing values are dropped.
  - The `genres`, `keywords`, `cast`, and `crew` columns, which are in JSON format, are parsed to extract the relevant information.
      - For `genres` and `keywords`, the 'name' of each item is extracted into a list.
      - For `cast`, the names of the top 5 actors are extracted.
      - For `crew`, the name of the director is extracted.

### 3\. Feature Engineering

  - To create a unified feature set for each movie, a **`tags`** column is created.
  - This column is a concatenation of the processed `genres`, `keywords`, `cast`, and `crew`.
  - All tags are converted to lowercase, and spaces within names (e.g., "Sam Worthington" becomes "SamWorthington") are removed to treat them as single entities.
  - **Stemming**: The text in the `tags` column is stemmed using the `PorterStemmer` from the NLTK library. This process reduces words to their root form (e.g., "actions", "acting" -\> "action"), which helps in consolidating similar words.

### 4\. Vectorization

  - The textual data in the `tags` column is converted into a numerical format that can be used for similarity calculations.
  - This is achieved using the `CountVectorizer` from scikit-learn, which converts the text into a sparse matrix of token counts.
  - The vectorizer is configured to consider the top 5000 most frequent words (features) and to remove English stop words.

### 5\. Similarity Calculation

  - The similarity between the movie vectors is calculated using **Cosine Similarity**. This metric measures the cosine of the angle between two vectors, which indicates how similar they are, regardless of their magnitude.
  - The result is a similarity matrix where each movie is compared against every other movie.

## 🚀 How to Use

The system provides a `recommend()` function that takes a movie title as input.

1.  **Input**: A movie title (e.g., `"Batman"`).
2.  **Process**:
      - The function finds the index of the input movie in the dataset.
      - It retrieves the corresponding similarity vector from the cosine similarity matrix.
      - It sorts the similarity scores in descending order and picks the top 5 most similar movies (excluding the input movie itself).
3.  **Output**: A list of the top 5 recommended movie titles.

Example usage:

```python
recommend("Batman")
```

## 📚 Libraries Used

  - `numpy`
  - `pandas`
  - `json`
  - `nltk` (for PorterStemmer)
  - `sklearn.feature_extraction.text.CountVectorizer`
  - `sklearn.metrics.pairwise.cosine_similarity`
