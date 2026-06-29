# German Used Car Scraping with BeautifulSoup

A lightweight data-collection and analysis project that scrapes used car listings for the German market and produces a cleaned dataset for exploratory analysis and visualization.

## Project overview

This repository contains a Jupyter notebook that:
- Scrapes vehicle listing pages from a dealer/aggregator site (country set to Germany).
- Extracts key fields such as make/model, price, description, year, mileage, fuel type and transmission.
- Builds a pandas DataFrame and demonstrates basic cleaning and preprocessing steps.
- Exports the cleaned dataset for further analysis or modelling.


## Features

- Page-by-page scraping with progress reporting (tqdm).
- Robust element extraction using BeautifulSoup with fallbacks for missing fields.
- Aggregation into a pandas DataFrame and sample export to CSV.
- Example data-cleaning steps to normalize price, mileage and year fields.
- Visualization-ready data for downstream analysis (matplotlib / seaborn).

## Requirements

Tested with Python 3.8+ and the following packages:
- beautifulsoup4
- lxml (recommended parser)
- numpy
- pandas
- tqdm
- matplotlib
- seaborn
- urllib (standard library)

You can install the main dependencies with pip:

pip install beautifulsoup4 lxml numpy pandas tqdm matplotlib seaborn

Or create a requirements file and install with:

pip install -r requirements.txt

## Usage

1. Clone the repository:
   git clone https://github.com/YassineSdk/german-car-scrapping-with-BS-.git
   cd german-car-scrapping-with-BS-

2. Open the notebook:
   - Launch Jupyter Notebook / JupyterLab and open `german_car_scrapping.ipynb`.

3. Run the cells in order:
   - The notebook fetches pages iteratively (controlled by the `pages` variable).
   - Monitor progress bars printed by tqdm.
   - After scraping, the notebook constructs a DataFrame and demonstrates cleaning steps.
   - Export the final DataFrame to CSV using `data.to_csv('german_cars.csv', index=False)`.

Notes:
- The notebook currently attempts to save the file; confirm the output filename and correct the extension if necessary (e.g., `.csv` instead of `.cvs`).
- If a long scrape is performed, consider limiting the `pages` variable for testing.

## Data fields (expected)

The notebook extracts the following columns (names may vary by page structure):
- name: vehicle make and model
- price: listing price (string, requires numeric parsing)
- description: trim/variant information
- trassmition: transmission (Automatic / Manual)
- model: production year
- milage: mileage string (e.g., `52,080 km`)
- fuel: fuel type (Petrol / Diesel / Hybrid / Electric / ...)

## Data cleaning suggestions

To prepare the data for analysis, apply these steps:

- Price:
  - Remove currency symbols and thousands separators (commas) then convert to numeric (float or int).
- Mileage:
  - Strip the `km` suffix and thousands separators, convert to integer.
- Year:
  - Convert the `model` column to integer and validate plausible ranges (e.g., 1980–current year).
- Missing values:
  - Inspect rows with NaNs and determine whether to drop or impute.
- Duplicates:
  - Remove exact duplicate listings and optionally deduplicate by unique listing URL (if available).
- Date parsing:
  - If listing dates are available, convert to datetime and compute vehicle age.

Example snippet (to run inside the notebook):

```python
# normalize price
data['price_num'] = (data['price']
    .str.replace(r'[^0-9.,]', '', regex=True)
    .str.replace(',', '')
    .astype(float))

# normalize mileage
data['milage_num'] = (data['milage']
    .str.replace(r'[^0-9]', '', regex=True)
    .astype(float).astype('Int64'))

# normalize year
data['year_num'] = pd.to_numeric(data['model'], errors='coerce').astype('Int64')
```

## Ethical scraping and legal notes

- Respect the website's robots.txt and terms of service before scraping.
- Rate-limit requests (add delays between page requests) to avoid overloading the target site.
- Use an appropriate User-Agent header and avoid aggressive parallel scraping.
- For commercial use, obtain explicit permission if required by the target website.

## Limitations

- The scraper relies on the HTML structure and CSS classes of the target site. Structural changes may break the parser and require updates to selectors.
- Some listings may hide information or use dynamic content loaded via JavaScript. The current approach uses urllib + BeautifulSoup and does not execute JavaScript.
- Long scrapes may produce incomplete reads or transient network errors; consider retry logic and error handling for production use.

## Improvements and next steps

- Add retry/backoff logic for transient network errors.
- Persist progress (e.g., write intermediate batches to disk) so long scrapes can resume after interruption.
- Use the site’s API (if available) instead of HTML scraping for more reliable data access.
- Add more robust logging and store a reference to the original listing URL for deduplication and provenance.
- Convert the notebook into a parameterized script or CLI for reproducible runs.


## Contact

If you have questions or need assistance, open an issue in the repository or contact the project maintainer listed on the repository profile.
