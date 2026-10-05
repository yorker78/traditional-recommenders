# Traditional Recommenders

Non-personalized, k-nearest neighbors, and SVD recommenders built and evaluated on four large rating datasets, plus the start of a content-based recommender for board games. Each approach is scored on how well it reproduces the ratings users actually gave, measured by R² on users held out of training.

This was a series of milestones over a semester of graduate coursework. More context is on my portfolio at [billyyork.com](https://billyyork.com).

## Results

Best R² for each approach:

| Dataset | Users | Ratings | Average rating | kNN (k = 500) | SVD (best k) |
| --- | --- | --- | --- | --- | --- |
| MovieLens 25M | 162,541 | 24.9M | 0.181 | 0.240 | **0.287** (50) |
| Netflix Prize | 473,816 | 51.0M | 0.131 | **0.219** | 0.187 (5) |
| Yahoo! Music R2 (subsample) | 200,000 | 76.3M | 0.107 | 0.218 | **0.242** (450) |
| BoardGameGeek | 332,079 | 18.9M | 0.260 | **0.269** | 0.176 (25) |

kNN beat the average-rating baseline everywhere, with diminishing returns after about 100 neighbors. SVD did best on MovieLens and Yahoo! Music but lost to kNN on Netflix and BoardGameGeek, and on BoardGameGeek it lost even to the average rating. My best guess is that the ideal number of components depends on how dense each user-item matrix is, since Netflix is the densest and peaked at 5 components, while Yahoo! Music is the sparsest and was still improving at 450.

## Notebooks

- [`01-rating-data`](notebooks/01-rating-data.ipynb) explores the size, density, and rating distributions of each dataset.
- [`02-non-personalized`](notebooks/02-non-personalized.ipynb) predicts ratings from each item's average rating, number of ratings, and number of positive ratings.
- [`03-knn`](notebooks/03-knn.ipynb) builds user-based collaborative filtering with k-nearest neighbors. Its appendix covers the memory problems I ran into joining neighbors back to their ratings at this scale, which reached more than 100GB before I found the fix, batching by query user.
- [`04-svd`](notebooks/04-svd.ipynb) predicts ratings with truncated SVD for 5 to 500 components.
- [`05-content-based`](notebooks/05-content-based.ipynb) joins the BoardGameGeek game data and engineers features for games and users.

## Data

The datasets aren't included here. The rating notebooks expect each dataset as a Feather file in the notebooks' directory, with `user_id`, `item_id`, and `rating` columns:

| File | Source |
| --- | --- |
| `movielens_25m.feather` | [MovieLens 25M](https://grouplens.org/datasets/movielens/25m/) from GroupLens |
| `netflix_prize.feather` | [Netflix Prize data](https://www.kaggle.com/datasets/netflix-inc/netflix-prize-data) |
| `yahoo_r2_songs.subsample.feather` | A 200,000-user subsample of Yahoo! Music R2, available through [Yahoo Webscope](https://webscope.sandbox.yahoo.com/) under its own data agreement |
| `boardgamegeek.feather` | User ratings from the [BoardGameGeek board games database](https://www.kaggle.com/datasets/threnjen/board-games-database-from-boardgamegeek) |

The content-based notebook uses the CSV files from the same BoardGameGeek dataset (`games.csv`, `mechanics.csv`, `themes.csv`, `user_ratings.csv`, and the others).

## Known limitations

- The content-based recommender isn't finished. The features are designed and the data is joined, but the kNN work took up enough of the semester that the model was never trained or evaluated.

## License

MIT
