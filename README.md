# Song Rating Analyzer

A Java command-line application that analyzes music rating data from CSV files. Supports statistical analysis, user similarity scoring, rating prediction, and song recommendations using collaborative filtering and k-means clustering.

## Features

- **Song Stats** — calculates mean and standard deviation of ratings per song using Welford's algorithm
- **User Similarity** — computes pairwise similarity scores between users via z-score normalization and Euclidean distance
- **Rating Prediction** — predicts how a user would rate songs they haven't heard based on their most similar peer
- **Song Recommendation** — clusters songs using k-means and recommends based on user-selected seed songs
- **Interactive Mode** — menu-driven interface for exploring CSV datasets without command-line flags

## Requirements

- Java 20+
- [Apache Commons CSV 1.10.0](https://commons.apache.org/proper/commons-csv/)

## Setup

1. Clone the repository
   ```bash
   git clone https://github.com/jjyang07/song-rating-analyzer.git
   cd song-rating-analyzer
   ```

2. Download [commons-csv-1.10.0.jar](https://search.maven.org/artifact/org.apache.commons/commons-csv/1.10.0/jar) and place it in the `lib/` folder

3. Compile
   ```bash
   javac -cp lib/commons-csv-1.10.0.jar src/MusicAnalyzer.java -d out/
   ```

## Usage

### Command-line mode

```bash
# Song stats
java -cp "out:lib/commons-csv-1.10.0.jar" MusicAnalyzer input.csv output.csv

# User similarity
java -cp "out:lib/commons-csv-1.10.0.jar" MusicAnalyzer input.csv output.csv -u

# Rating prediction
java -cp "out:lib/commons-csv-1.10.0.jar" MusicAnalyzer input.csv output.csv -p

# Song recommendations (one or more seed songs)
java -cp "out:lib/commons-csv-1.10.0.jar" MusicAnalyzer input.csv output.csv -r "Song A" "Song B"
```

> **Windows users:** replace `"out:lib/..."` with `"out;lib/..."` in the commands above.

### Interactive mode

```bash
java -cp "out:lib/commons-csv-1.10.0.jar" MusicAnalyzer -i
```

Launches a menu-driven interface to load a folder of CSV files and run any analysis without command-line flags.

## Input Format

A CSV file with three columns and no header:

```
song_name,username,rating
```

- `song_name` — title of the song
- `username` — name of the user who submitted the rating
- `rating` — integer from 1 to 5

**Example** (`sample-data/example.csv`):
```
Bohemian Rhapsody,alice,5
Hotel California,alice,3
Stairway to Heaven,alice,4
Bohemian Rhapsody,bob,4
Hotel California,bob,5
Stairway to Heaven,bob,2
Bohemian Rhapsody,carol,3
Hotel California,carol,4
Stairway to Heaven,carol,5
```

## Output Format

Each mode writes a CSV file:

| Mode | Columns |
|---|---|
| Song Stats | `song`, `number of ratings`, `mean`, `standard deviation` |
| User Similarity | `name1`, `name2`, `similarity` |
| Prediction | `song`, `user`, `predicted rating` |
| Recommendation | `user choice`, `recommendation` |

## Project Structure

```
song-rating-analyzer/
├── src/
│   └── MusicAnalyzer.java
├── lib/
│   └── commons-csv-1.10.0.jar
├── sample-data/
│   └── example.csv
└── README.md
```

## How It Works

**Song Stats** uses Welford's online algorithm to compute mean and standard deviation in a single pass, which is more numerically stable than the naive two-pass approach.

**User Similarity** normalizes each user's ratings to z-scores to account for rating bias (e.g. a user who always rates high vs. one who rates conservatively), then computes Euclidean distance between users across shared songs. Lower distance means higher similarity.

**Rating Prediction** finds the most similar user who has rated a given song, then maps their z-score back into the target user's rating scale.

**Song Recommendation** builds a full rating matrix from actual and predicted ratings, normalizes by song, then runs k-means clustering seeded at user-selected songs. Songs that cluster with a seed are returned as recommendations.
