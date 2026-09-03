# 🎬 Movie Recommender

A movie recommendation web application built with **Streamlit**, **FastAPI**, **TMDB**, and a local **TF-IDF similarity model**.

The application lets users search for movies, view movie details, and discover similar movies using two recommendation approaches:

- 🔎 **TF-IDF recommendations** — finds movies with similar text/content features from the local dataset.
- 🎭 **Genre recommendations** — finds popular movies belonging to the selected movie's first genre.

## ✨ Features

- Search movies by title keyword
- Autocomplete-style movie suggestions
- Display movie posters in a responsive grid
- Home feeds for:
  - Trending
  - Popular
  - Top Rated
  - Now Playing
  - Upcoming
- Detailed movie information
- Movie poster and backdrop images from TMDB
- Release date and genre information
- TF-IDF-based similar movie recommendations
- Genre-based recommendations
- FastAPI backend with REST endpoints
- Streamlit frontend
- Cached search requests for smoother autocomplete
- URL-based navigation between home and movie-detail views

## 🏗️ Project Architecture

```text
User
 │
 ▼
Streamlit Frontend (app.py)
 │
 │ HTTP requests
 ▼
FastAPI Backend (main.py)
 │
 ├── TMDB API
 │    ├── Movie search
 │    ├── Movie details
 │    ├── Trending / Popular / Top Rated
 │    └── Genre discovery
 │
 └── Local Recommendation Model
      ├── df.pkl
      ├── indices.pkl
      ├── tfidf.pkl
      └── tfidf_matrix.pkl
```

## 📁 Project Structure

```text
movie-recommendation/
│
├── app.py
├── main.py
├── requirements.txt
├── movies_metadata.csv
│
├── df.pkl
├── indices.pkl
├── tfidf.pkl
└── tfidf_matrix.pkl
```

### Main files

| File | Purpose |
|---|---|
| `app.py` | Streamlit frontend and user interface |
| `main.py` | FastAPI backend and recommendation API |
| `movies_metadata.csv` | Movie dataset used to build the recommendation resources |
| `df.pkl` | Serialized movie DataFrame |
| `indices.pkl` | Movie-title-to-index mapping |
| `tfidf.pkl` | Saved TF-IDF vectorizer |
| `tfidf_matrix.pkl` | Saved TF-IDF matrix |
| `requirements.txt` | Python dependencies |

## 🔧 Technologies Used

- **Python**
- **Streamlit** — frontend UI
- **FastAPI** — backend REST API
- **TMDB API** — movie information, posters, backdrops, search, and discovery
- **Pandas** — data handling
- **NumPy** — numerical operations
- **SciPy** — sparse matrix operations
- **scikit-learn** — TF-IDF and similarity calculations
- **HTTPX / Requests** — HTTP communication
- **python-dotenv** — environment-variable management
- **Uvicorn** — FastAPI server

## 🚀 Getting Started

### 1. Clone the repository

```bash
git clone https://github.com/Omer001186/Movie-recommendation.git
cd Movie-recommendation
```

### 2. Create a virtual environment

Windows:

```bash
python -m venv venv
venv\Scripts\activate
```

macOS/Linux:

```bash
python3 -m venv venv
source venv/bin/activate
```

### 3. Install dependencies

```bash
pip install -r requirements.txt
```

The project uses FastAPI, Uvicorn, Streamlit, Pandas, NumPy, SciPy, scikit-learn, HTTPX, and python-dotenv. Make sure your Python version is compatible with the pinned package versions.

### 4. Configure the TMDB API key

Create a `.env` file in the project root:

```env
TMDB_API_KEY=your_tmdb_api_key
```

The FastAPI backend requires `TMDB_API_KEY` at startup.

## ▶️ Running the Application

The project has two parts: the FastAPI backend and the Streamlit frontend.

### Start the FastAPI backend

Open a terminal in the project directory:

```bash
uvicorn main:app --reload --port 8000
```

The API will normally be available at:

```text
http://127.0.0.1:8000
```

You can check the health endpoint:

```text
GET /health
```

Expected response:

```json
{
  "status": "ok"
}
```

### Start the Streamlit frontend

Open another terminal:

```bash
streamlit run app.py
```

Then open the local Streamlit URL shown in the terminal.

## 🔑 Environment Variables

| Variable | Required | Description |
|---|---|---|
| `TMDB_API_KEY` | Yes | API key used to access TMDB |

Do **not** commit your `.env` file or expose your TMDB API key publicly.

## 🔌 API Endpoints

### Health check

```http
GET /health
```

Checks whether the backend is running.

### Home feed

```http
GET /home?category=popular&limit=24
```

Supported categories:

```text
trending
popular
top_rated
now_playing
upcoming
```

### TMDB movie search

```http
GET /tmdb/search?query=batman
```

Returns movie search results from TMDB.

### Movie details

```http
GET /movie/id/{tmdb_id}
```

Returns details such as:

- Movie title
- Overview
- Release date
- Poster
- Backdrop
- Genres

### Genre recommendations

```http
GET /recommend/genre?tmdb_id=123&limit=18
```

Finds popular movies from the selected movie's first genre.

### TF-IDF recommendations

```http
GET /recommend/tfidf?title=Inception&top_n=10
```

Returns locally calculated similar movies and their similarity scores.

### Combined movie search

```http
GET /movie/search?query=Inception&tfidf_top_n=12&genre_limit=12
```

Returns:

- Movie details
- TF-IDF recommendations
- Genre recommendations

## 🧠 How Recommendations Work

### TF-IDF Recommendation

The backend loads a saved TF-IDF matrix and title-index mapping from the pickle files.

For a selected movie:

1. The movie title is normalized.
2. Its index is found using `indices.pkl`.
3. Its TF-IDF vector is retrieved.
4. Similarity scores are calculated against the movie matrix.
5. Movies are sorted by descending similarity.
6. The highest-scoring movies are returned.

The selected movie itself is excluded from the recommendation results.

### Genre Recommendation

The genre recommendation system:

1. Gets the selected movie's details from TMDB.
2. Takes the first genre associated with the movie.
3. Uses TMDB's movie discovery endpoint for that genre.
4. Sorts results by popularity.
5. Removes the currently selected movie.

## 🖥️ Frontend Flow

```text
Search movie
     │
     ▼
TMDB search
     │
     ├── Suggestions
     │
     └── Matching poster results
              │
              ▼
        Select a movie
              │
              ▼
         Movie details
              │
              ├── TF-IDF similar movies
              │
              └── Genre recommendations
```

The Streamlit application also supports a home feed with selectable categories and configurable poster-grid columns.

## ⚙️ Configuration

The Streamlit frontend is configured to call the deployed API by default:

```python
API_BASE = "https://movie-rec-466x.onrender.com"
```

For local development, change the configuration to your local FastAPI server:

```python
API_BASE = "http://127.0.0.1:8000"
```

Keep only the endpoint you want to use in your actual `app.py` configuration.

## 📌 Notes

- The recommendation pickle files must be present for TF-IDF recommendations to work.
- The local dataset must contain a `title` column.
- TMDB is used for current movie metadata and images.
- If a movie cannot be matched to the local TF-IDF dataset, the combined recommendation endpoint attempts a fallback using the search query.
- If TF-IDF recommendations are unavailable, the frontend can fall back to genre recommendations.

## 🛠️ Troubleshooting

### `TMDB_API_KEY missing`

Make sure `.env` exists in the project root and contains:

```env
TMDB_API_KEY=your_tmdb_api_key
```

Then restart the FastAPI server.

### `Title not found in local dataset`

The selected TMDB movie may not exist in the local recommendation dataset. Check that the movie title is present in the data used to create `df.pkl` and `indices.pkl`.

### Frontend cannot connect to backend

Make sure FastAPI is running and that `API_BASE` in `app.py` points to the correct backend URL.

For local development:

```python
API_BASE = "http://127.0.0.1:8000"
```

### SciPy / scikit-learn installation issues

The project pins versions of NumPy, SciPy, Pandas, and scikit-learn. If installation fails, use a Python version supported by those package versions and install inside a fresh virtual environment.



## 🙌 Acknowledgements

- Movie information and movie images are provided through **TMDB**.
- The recommendation system uses TF-IDF-based text similarity and genre-based discovery.
