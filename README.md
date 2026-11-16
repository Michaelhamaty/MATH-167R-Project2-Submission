# Where a foodie can stay for a meal in New Orleans

Michael Hamaty, MATH 167R, Project 2

**[Open the report](https://michaelhamaty.github.io/MATH-167R-Project2-Submission/yelp_new_orleans_project.html)**

The page has the maps, the charts, the tables, and the R code. Click "View R code" under a chart to see the code. The same page is saved in this repo as [docs/yelp_new_orleans_project.html](docs/yelp_new_orleans_project.html), and you can open that file in a browser.

## Research question

Where can a foodie find local, creative places to stay for a meal, a drink, or seafood in New Orleans, which ten places rank first, and which neighborhood is best when an area needs at least five of those places?

## What I found

- Leonidas comes out first on Yelp stars: 7 places, average 4.43. The French Quarter has 37 places and a lower average, 3.97.
- On the adjusted score, which compares each review with the reviewer's own usual rating, the French Quarter is first, at -0.14 over 23 places.
- The adjusted top 10 starts with Roux Carre. Four of the ten are in the French Quarter, and two are in Leonidas.

## The foodie set

A restaurant stays in the set when three things are true. It has at least one of these Yelp categories: American (new), cocktail bars, cafes, or seafood. Its name appears at most twice among all New Orleans restaurants, so Ruby Slipper and Dat Dog come out along with Waffle House. At least 5% of its reviews use one of these words: fusion, handmade, courtyard, mural, seasonal, inventive, or omakase. That leaves 108 restaurants.

A "New Orleans restaurant" is a business with `state == "LA"`, `city == "New Orleans"`, and a `categories` text that contains "Restaurants". That gives **2,259 restaurants** and **476,421 reviews**.

## What is in this repo

| Path | What it holds |
|---|---|
| [yelp_new_orleans_project.qmd](yelp_new_orleans_project.qmd) | Source of the report |
| [docs/](docs/) | The rendered report, with the graphs inside the file |
| [R/](R/) | The scripts, in the order below |
| [data/new_orleans/](data/new_orleans/) | Saved results: filtered Yelp data (RDS) and the tables the report reads (CSV) |
| [data/geo/](data/geo/) | Neighborhood boundaries from Data.NOLA.gov |
| [figures/](figures/) | The maps and charts as PNG files |
| [prompts/PROMPT_HISTORY.md](prompts/PROMPT_HISTORY.md) | The prompts I gave the agent, with what I decided after each |

The scripts:

1. [R/download.sh](R/download.sh) downloads the two Yelp files from Kaggle
2. [R/import.R](R/import.R) loads the Yelp files and keeps New Orleans restaurants
3. [R/categories.R](R/categories.R) builds the 108 and joins their reviews
4. [R/places.R](R/places.R) checks the points against the Census city boundary
5. [R/areas.R](R/areas.R) compares neighborhoods with census tracts
6. [R/reviewers.R](R/reviewers.R) adjusts each review for the reviewer's usual score, then builds the top 10 lists, the scatter, and the top 10 map

## Why the raw files are not here

The raw Yelp JSON is not committed. The review file is about 5 GB, which is over GitHub's file size limit. The saved RDS files in `data/new_orleans/` are enough to run everything after the import.

The reviews file is about 70 MB, so it is stored with Git LFS. Run `git lfs install` before cloning. Read the saved files with:

```r
business <- readRDS("data/new_orleans/business_new_orleans.rds")
reviews  <- readRDS("data/new_orleans/reviews_new_orleans.rds")
```

## Getting the raw data

To recreate the data, run these from the repo folder:

```bash
bash R/download.sh        # needs the kaggle CLI + API key
Rscript R/import.R
Rscript R/categories.R
Rscript R/places.R        # downloads a Census place file the first time
Rscript R/areas.R         # downloads census tracts the first time
Rscript R/reviewers.R     # adjusted scores, top 10 lists, scatter, and map
```

The Yelp Open Dataset is also available from https://www.yelp.com/dataset. Put the unzipped files in `data/raw/`. To rebuild the report, run `quarto render yelp_new_orleans_project.qmd --output-dir docs`.

## Handling the large files

- My laptop has 8 GB of RAM, so I could not load the 5 GB review file all at once. `stream_in()` reads it in pages of 100,000 reviews and only keeps the ones for New Orleans restaurants.
- Only the needed columns are kept.
- Disk space was tight (about 12 GB free), so I only downloaded the business and review files, and deleted the raw files after saving the RDS files.
- The import takes about 7 minutes.
