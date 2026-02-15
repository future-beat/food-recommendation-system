# Health-Oriented Food Recommendation System

A content-based recipe recommendation system that finds nutritionally similar recipes and ranks them by healthiness. Given any recipe from a dataset of 339,000+ entries, it identifies the top 8 healthiest alternatives using k-Nearest Neighbors and a weighted nutritional scoring formula.

## How It Works

1. **Normalize** nutritional attributes (calories, fat, protein, fiber, etc.) to a common scale using MinMaxScaler
2. **Find similar recipes** using k-Nearest Neighbors (k=50) with Euclidean distance across 9 nutritional dimensions
3. **Score healthiness** with a weighted formula that rewards fiber and protein while penalizing excess calories, fat, sodium, and sugar
4. **Return the top 8** healthiest recipes from the similar set

## Dataset

The system uses two datasets in Parquet format (not included in the repo due to size):

- **recipes.parquet** — 522,517 recipes with 28 columns including nutritional info, instructions, and metadata (339,606 after cleaning)
- **reviews.parquet** — User reviews with ratings (1–5 scale)

Each recipe includes: Calories, FatContent, SaturatedFatContent, CholesterolContent, SodiumContent, CarbohydrateContent, FiberContent, SugarContent, and ProteinContent.

## Requirements

- Python 3.7+
- numpy
- pandas
- scikit-learn

Install dependencies:

```bash
pip install numpy pandas scikit-learn
```

## Usage

1. Open `food_recommendation.ipynb` in Jupyter Notebook or Google Colab
2. If using Colab, mount Google Drive and update the data paths in the notebook to point to your Parquet files
3. Run all cells sequentially
4. Change `input_recipe_id` to explore recommendations for different recipes

### Example

```python
# Initialize the recommender
recommender = RecipeRecommender(nutritional_df, cols_to_divide)

# Find the 50 most similar recipes to recipe #123456
result = recommender.find_closest_recipes(123456, k=50)

# Score and select the top 8 healthiest
result["healthiness_score"] = result.apply(compute_healthiness_score, axis=1)
top_8 = result.nlargest(8, "healthiness_score")
```

Sample output for "Spinach and Tomatoes" (ID 123456, score 7.99):

| Recipe | Score |
|---|---|
| Light Refried Black Beans | 8.58 |
| Lemon Asparagus II | 8.57 |
| Wrapped Vegetables | 8.40 |
| Awesome Pinto Beans | 8.38 |
| Spinach, Lemon and Lentil Soup | 8.30 |

## Healthiness Score Weights

| Nutrient | Weight | Effect |
|---|---|---|
| FiberContent | +0.9 | Most rewarded |
| ProteinContent | +0.2 | Rewarded |
| Calories | −0.002 | Penalized |
| FatContent | −0.04 | Penalized |
| SaturatedFatContent | −0.1 | Strongly penalized |
| CholesterolContent | −0.01 | Penalized |
| SodiumContent | −0.0015 | Penalized |
| CarbohydrateContent | −0.02 | Penalized |
| SugarContent | −0.045 | Penalized |

## Project Structure

```
├── food_recommendation.ipynb   # Full implementation notebook
├── Project Report B.pdf        # Detailed project report
├── data/                       # Dataset directory (git-ignored)
│   ├── recipes.parquet
│   └── reviews.parquet
└── README.md
```

## License

This project is for educational and research purposes.
