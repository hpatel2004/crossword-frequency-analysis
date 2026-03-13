# Crossword Frequency Analysis - AI Agent Instructions

## Project Overview
This project analyzes crossword puzzle frequencies by scraping and parsing grid data from Crosshare-embedded puzzles on web pages. The core workflow involves web scraping with Selenium to extract puzzle JSON data, then processing the flat grid string into structured data for frequency analysis.

## Key Patterns & Conventions

### Web Scraping with Selenium
- Use Selenium with Chrome WebDriver for scraping Crosshare iframes
- Set `page_load_strategy = "eager"` for faster page loads when only initial HTML is needed
- Target iframes with "crosshare" in the `src` attribute
- Extract puzzle data from `__NEXT_DATA__` script tag's `innerHTML`
- Parse JSON to access `data["props"]["pageProps"]["puzzle"]`

Example from `grid_parsing.ipynb`:
```python
options = Options()
options.page_load_strategy = "eager"
driver = webdriver.Chrome(options=options)
driver.get(url)

iframes = driver.find_elements(By.TAG_NAME, "iframe")
for iframe in iframes:
    src = iframe.get_attribute("src")
    if not src or "crosshare" not in src:
        continue
    
    driver.switch_to.frame(iframe)
    script = driver.find_element(By.ID, "__NEXT_DATA__")
    json_text = script.get_attribute("innerHTML")
    data = json.loads(json_text)
    puzzle = data["props"]["pageProps"]["puzzle"]
```

### Grid Data Structure
- Grid is stored as a flat string where each character represents a cell
- Use `puzzle["size"]["cols"]` to determine grid width
- Process grid in chunks of `cols` to get rows: `for i in range(0, len(grid), cols): row = grid[i:i+cols]`

### Development Workflow
- Use Jupyter notebooks (`.ipynb`) for exploratory development and testing
- Create separate cells for testing with sample data (e.g., `sample_grid = []`)
- Run cells sequentially to maintain state and debug incrementally
- Install dependencies via `pip install selenium` (ChromeDriver handled automatically)

### Code Organization
- Keep scraping logic in dedicated notebook cells
- Use Python standard library for JSON parsing and string manipulation
- Avoid complex dependencies; prefer simple, readable code for data processing

## Common Tasks
- When adding new puzzle sources, follow the iframe scraping pattern
- For grid analysis, always convert flat string to row-based structure using cols
- Test parsing logic with sample grids in separate cells before full scraping runs