# Grocery List Optimizer

A Python-based smart grocery shopping assistant that finds the cheapest combination of stores for your grocery list and calculates the travel distance to each store. Built with Jupyter Notebooks and a Gradio web interface.

---

## Problem Statement

Grocery prices vary across stores, and shoppers often do not know which store offers the best price for each item. Visiting multiple stores can also add travel time. This project helps users find the cheapest store for each item on their list while also showing the travel distance to each store from their current location.

---

## Objectives

- Accept a user-defined grocery list and current location (latitude, longitude).
- Identify the cheapest store for each item across available stores.
- Calculate the geodesic distance from the user to each required store.
- Display a summary of the optimized shopping plan.
- Visualize store locations on an interactive map.
- Allow the user to download the optimized plan as a CSV or PDF file.

---

## Key Features

- **Price optimization** — greedy selection of the lowest-priced store per item
- **Distance calculation** — geodesic distance computed using the `geopy` library
- **Interactive Gradio UI** — browser-based interface; no command-line knowledge required
- **Interactive map** — store and user locations plotted on an OpenStreetMap tile layer via `folium`
- **Export to CSV and PDF** — download the optimized grocery plan in both formats
- **Sample datasets included** — pre-built grocery item prices and store coordinates for three Mumbai-area stores

---

## Technologies Used

| Category        | Technology / Library             |
|-----------------|----------------------------------|
| Language        | Python 3.12.7                    |
| Notebook        | Jupyter Notebook                 |
| Data handling   | pandas 2.2.3                     |
| Distance calc   | geopy 2.4.1                      |
| Map rendering   | folium 0.20.0                    |
| Web interface   | Gradio 5.17.1                    |
| PDF generation  | fpdf 1.7.2                       |
| Environment     | Miniconda (conda)                |

---

## Project Structure

```
Grocery List Optimizer/
├── Untitled.ipynb          # Dataset creation notebook
│                           #   Creates grocery_items.csv and store_locations.csv
├── Untitled1.ipynb         # Main application notebook
│                           #   Data loading → price optimization → distance
│                           #   calculation → Gradio UI → folium map → CSV/PDF export
├── data/
│   ├── grocery_items.csv   # Item prices per store (source data)
│   └── store_locations.csv # Store GPS coordinates (source data)
├── requirements.txt        # Python package dependencies
└── README.md               # This file
```

> **Note:** `data/optimized_list.csv` and `data/optimized_list.pdf` are generated at runtime when the optimizer is executed. They are not tracked in version control.

---

## Dataset Description

### `data/grocery_items.csv`

Stores the price of each grocery item at each store.

| Column  | Description                   |
|---------|-------------------------------|
| `item`  | Name of the grocery item      |
| `price` | Price in Indian Rupees (₹)    |
| `store` | Store where the price applies |

Sample items: Milk, Bread, Eggs, Tomato  
Sample stores: D-Mart, Reliance Fresh, Big Bazaar

> **Dataset source:** Sample data created manually for demonstration purposes. Coordinates are approximate values for a Mumbai suburban area. Prices are fictional and for illustrative use only.

### `data/store_locations.csv`

Stores the GPS coordinates of each store.

| Column      | Description               |
|-------------|---------------------------|
| `store`     | Store name                |
| `latitude`  | Latitude (decimal degrees)|
| `longitude` | Longitude (decimal degrees)|

---

## Algorithm / Optimization Technique

The optimizer uses a **greedy per-item cheapest-store selection** approach:

1. For each item in the user's grocery list, all store prices are looked up in `grocery_items.csv`.
2. The store with the **minimum price** for that item is selected (`pandas idxmin()`).
3. Items are grouped by their selected store.
4. The **geodesic distance** (straight-line surface distance) from the user's GPS coordinates to each required store is computed using `geopy.distance.geodesic`.
5. Total cost and total distance are aggregated and returned to the user.

This approach minimizes price per item independently. It does not solve a combined price-and-travel optimization (e.g., it does not consolidate stores to minimize total trips).

---

## Installation

### Prerequisites

- Python 3.10 or later (tested on 3.12.7)
- `pip` or `conda` package manager

### 1. Clone the repository

```bash
git clone https://github.com/GaureshShinde25/Grocery-List-Optimizer.git
cd Grocery-List-Optimizer
```

### 2. Create and activate a virtual environment (recommended)

```bash
# Using venv
python -m venv .venv
# Windows
.venv\Scripts\activate
# macOS / Linux
source .venv/bin/activate
```

Or using conda:

```bash
conda create -n grocery-optimizer python=3.12
conda activate grocery-optimizer
```

### 3. Install dependencies

```bash
pip install -r requirements.txt
```

---

## How to Run the Project

### Option A — Run in Jupyter Notebook (recommended)

1. Start Jupyter:
   ```bash
   jupyter notebook
   ```
2. Open **`Untitled1.ipynb`** (the main application notebook).
3. Run all cells in order (`Kernel → Restart & Run All`).
4. A Gradio interface will launch automatically in the notebook output and at `http://127.0.0.1:7860` (or a nearby port).

### Option B — Run the dataset creation notebook first (if needed)

If `data/grocery_items.csv` or `data/store_locations.csv` are missing:

1. Open **`Untitled.ipynb`**.
2. Run all cells to regenerate the datasets.

---

## How to Use the Application

Once the Gradio interface is running:

1. **Enter your grocery list** — type item names separated by commas, e.g.:
   ```
   Milk, Eggs, Tomato
   ```
2. **Enter your latitude and longitude** — defaults are pre-filled with a sample Mumbai location (`19.1200, 72.8800`).
3. Click **Submit**.
4. The interface returns:
   - A table showing the cheapest store, price, and distance for each item.
   - A downloadable CSV file of the optimized plan.
   - A downloadable PDF file of the optimized plan.
   - An interactive folium map showing your location (blue marker) and the required stores (green markers).

---

## Example Input and Output

**Input:**

| Field          | Value              |
|----------------|--------------------|
| Grocery list   | `Milk, Eggs, Tomato` |
| Latitude       | `19.1200`          |
| Longitude      | `72.8800`          |

**Output:**

| Item   | Store          | Price (₹) | Distance (km) |
|--------|----------------|-----------|---------------|
| Milk   | D-Mart         | 50        | 0.27          |
| Eggs   | Reliance Fresh | 58        | 0.11          |
| Tomato | D-Mart         | 25        | 0.27          |
| TOTAL  |                | 133       | 0.38          |

---

## Current Limitations

- The sample dataset contains only 4 grocery items (Milk, Bread, Eggs, Tomato) and 3 stores. Items not present in the dataset are silently skipped.
- Optimization is greedy (cheapest price per item) and does not account for a combined cost-vs-travel tradeoff.
- Store coordinates are fixed sample values; real-world use requires accurate GPS data.
- The application runs locally; it is not deployed as a public web service.
- The Gradio interface requires all cells in `Untitled1.ipynb` to be run sequentially from top to bottom in the same kernel session.

---

## Future Improvements

- Expand the dataset to include more items, stores, and real pricing data.
- Implement a combined optimization that minimizes total cost plus estimated travel cost.
- Add support for uploading a custom grocery list CSV.
- Allow users to add new items and stores directly through the UI.
- Deploy the Gradio app publicly (e.g., Hugging Face Spaces).
- Add store opening hours and real-time pricing via an API.

---

## License

This project was created for learning and demonstration purposes. No license is currently specified.
