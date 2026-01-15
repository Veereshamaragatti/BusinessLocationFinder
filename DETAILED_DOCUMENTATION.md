# 📍 GeoClusterEngine - Complete Project Documentation

## A Detailed Guide to Understanding the Business Location Finder System

---

## Table of Contents
1. [Project Overview](#1-project-overview)
2. [What Problem Does This Solve?](#2-what-problem-does-this-solve)
3. [How the System Works (High-Level Flow)](#3-how-the-system-works-high-level-flow)
4. [Project Structure](#4-project-structure)
5. [Module-by-Module Breakdown](#5-module-by-module-breakdown)
6. [Data Flow Explained](#6-data-flow-explained)
7. [Algorithms & Scoring Logic](#7-algorithms--scoring-logic)
8. [Running the Application](#8-running-the-application)
9. [Output Files Explained](#9-output-files-explained)
10. [Dashboard Features](#10-dashboard-features)
11. [Dependencies](#11-dependencies)
12. [Configuration](#12-configuration)

---

## 1. Project Overview

**GeoClusterEngine** (also known as BusinessLocationFinder) is a geospatial analysis platform that helps entrepreneurs and business analysts find the best locations to open a new business. 

### Key Capabilities:
- **Fetches real data** from OpenStreetMap (POIs - Points of Interest)
- **Cleans and processes** the data
- **Clusters POIs** using machine learning (DBSCAN)
- **Scores locations** based on multiple factors (demand, competition, accessibility, infrastructure)
- **Generates visualizations** (interactive maps, heatmaps)
- **Creates PDF reports** with recommendations
- **Supports multi-city comparison** for franchise expansion planning

---

## 2. What Problem Does This Solve?

When opening a new business (like a cafe, restaurant, gym, etc.), location is critical. This system answers:

1. **"Where should I open my business?"** - Finds optimal locations based on data
2. **"How much competition will I face?"** - Analyzes existing competitors
3. **"Is there demand in this area?"** - Evaluates supporting businesses nearby
4. **"Is the location accessible?"** - Checks transport links
5. **"What type of business suits this area?"** - Recommends business types

---

## 3. How the System Works (High-Level Flow)

```
┌─────────────────────────────────────────────────────────────────────────┐
│                              USER INPUT                                  │
│    (City name or Coordinates, Business Type, Search Radius, Weights)    │
└─────────────────────────────────────────────────────────────────────────┘
                                    │
                                    ▼
┌─────────────────────────────────────────────────────────────────────────┐
│  STEP 1: DATA FETCHING (fetch_data.py)                                  │
│  - Geocodes city name to coordinates                                    │
│  - Fetches POIs (restaurants, cafes, banks, schools, etc.)              │
│  - Uses OpenStreetMap via OSMnx library                                 │
│  - Caches results for performance                                       │
└─────────────────────────────────────────────────────────────────────────┘
                                    │
                                    ▼
┌─────────────────────────────────────────────────────────────────────────┐
│  STEP 2: DATA CLEANING (clean_data.py)                                  │
│  - Removes duplicates                                                   │
│  - Validates coordinates                                                │
│  - Normalizes category names                                            │
│  - Groups categories (cafe → cafe, coffee_shop → cafe, etc.)            │
└─────────────────────────────────────────────────────────────────────────┘
                                    │
                                    ▼
┌─────────────────────────────────────────────────────────────────────────┐
│  STEP 3: CLUSTERING (clustering.py)                                     │
│  - Uses DBSCAN with haversine distance                                  │
│  - Identifies natural business clusters                                 │
│  - Detects hotspots (high-density areas)                                │
│  - Detects sparse regions (underserved areas)                           │
└─────────────────────────────────────────────────────────────────────────┘
                                    │
                                    ▼
┌─────────────────────────────────────────────────────────────────────────┐
│  STEP 4: LOCATION SCORING (scoring.py)                                  │
│  - Creates grid of candidate locations                                  │
│  - For each candidate, calculates:                                      │
│    • Demand Score (based on supporting POIs nearby)                     │
│    • Competition Score (inverse of competitor density)                  │
│    • Accessibility Score (transport links)                              │
│    • Infrastructure Score (banks, hospitals, schools)                   │
│  - Computes weighted final score                                        │
│  - Ranks all candidates                                                 │
└─────────────────────────────────────────────────────────────────────────┘
                                    │
                                    ▼
┌─────────────────────────────────────────────────────────────────────────┐
│  STEP 5: VISUALIZATION (visualize.py)                                   │
│  - Creates interactive Folium maps                                      │
│  - Generates heatmaps (density & competition)                           │
│  - Exports static PNG images                                            │
│  - Shows recommended locations with rankings                            │
└─────────────────────────────────────────────────────────────────────────┘
                                    │
                                    ▼
┌─────────────────────────────────────────────────────────────────────────┐
│  STEP 6: REPORTING (report_generator.py)                                │
│  - Generates PDF reports with executive summary                         │
│  - Documents methodology                                                │
│  - Lists top 10 recommended locations                                   │
│  - Provides actionable insights                                         │
└─────────────────────────────────────────────────────────────────────────┘
                                    │
                                    ▼
┌─────────────────────────────────────────────────────────────────────────┐
│                             OUTPUT                                       │
│  - CSV files (raw_data, cleaned_data, clusters, location_scores)        │
│  - HTML maps (interactive)                                               │
│  - PNG images (static)                                                   │
│  - PDF report                                                            │
│  - Streamlit dashboard (interactive exploration)                         │
└─────────────────────────────────────────────────────────────────────────┘
```

---

## 4. Project Structure

```
BusinessLocationFinder/
├── app.py                    # Main Streamlit web application
├── main.py                   # Command-line interface (CLI)
├── pyproject.toml            # Python dependencies and project config
├── uv.lock                   # Lock file for dependencies
├── README.md                 # Project readme
├── DETAILED_DOCUMENTATION.md # This detailed documentation
│
├── modules/                  # Core business logic
│   ├── __init__.py
│   ├── fetch_data.py         # Data fetching from OpenStreetMap
│   ├── clean_data.py         # Data cleaning and preprocessing
│   ├── clustering.py         # DBSCAN & KMeans clustering
│   ├── visualize.py          # Map creation and visualization
│   ├── scoring.py            # Location scoring algorithms
│   ├── recommendations.py    # Business type recommendations
│   ├── report_generator.py   # PDF report generation
│   ├── business_suggestion.py # Business viability analysis
│   └── simulation.py         # Agent-based simulation
│
├── data/                     # Generated data files
│   ├── raw_data.csv          # Original fetched POI data
│   ├── cleaned_data.csv      # Processed and cleaned data
│   ├── clusters.csv          # POIs with cluster labels
│   └── location_scores.csv   # Scored candidate locations
│
├── maps/                     # Generated map files
│   ├── all_pois_map.html     # Interactive map of all POIs
│   ├── cluster_map.html      # Cluster visualization
│   ├── heatmap.html          # POI density heatmap
│   ├── competition_heatmap.html # Competition analysis
│   └── recommendations_map.html # Top locations
│
├── cache/                    # Cached data for performance
│   ├── osmnx/               # OSMnx cache
│   └── bulk/                # Bulk fetch cache
│
└── .streamlit/
    └── config.toml          # Streamlit configuration
```

---

## 5. Module-by-Module Breakdown

### 5.1 `fetch_data.py` - Data Fetching Module

**Purpose:** Fetches Points of Interest (POIs) from OpenStreetMap.

**Key Class: `DataFetcher`**

```python
# Example usage:
fetcher = DataFetcher("Bangalore", radius_km=5.0)
center = fetcher.get_city_center()  # Returns (12.97194, 77.59369)
all_pois = fetcher.fetch_all_pois(['cafe', 'restaurant', 'bank'])
```

**What it does:**
1. **Geocodes city name** → Gets latitude/longitude coordinates
2. **Fetches POIs** by category using OSMnx (which queries Overpass API)
3. **Supports 20+ POI categories:** shop, restaurant, cafe, supermarket, bank, hospital, pharmacy, school, hotel, gym, etc.
4. **Caches results** to avoid repeated API calls
5. **Saves data** to CSV/GeoJSON

**POI Categories Mapping:**
```python
POI_CATEGORIES = {
    'cafe': {'amenity': 'cafe'},
    'restaurant': {'amenity': 'restaurant'},
    'bank': {'amenity': 'bank'},
    'hospital': {'amenity': 'hospital'},
    'supermarket': {'shop': 'supermarket'},
    'gym': {'leisure': 'fitness_centre'},
    # ... and more
}
```

---

### 5.2 `clean_data.py` - Data Cleaning Module

**Purpose:** Cleans and preprocesses raw POI data.

**Key Class: `DataCleaner`**

**What it does:**
1. **Extracts coordinates** from geometry objects
2. **Removes invalid coordinates** (missing, out of range)
3. **Removes duplicates** (same location + name)
4. **Normalizes categories:**
   - `cafe`, `coffee`, `coffee_shop` → `cafe`
   - `restaurant`, `fast_food` → `restaurant`
   - etc.
5. **Groups categories:**
   - `cafe` → `cafe`
   - `supermarket`, `shop` → `retail`
   - `bank`, `atm` → `finance`
   - `hospital`, `pharmacy` → `healthcare`
6. **Adds derived fields** (rating, footfall_estimate placeholders)

**Category Mapping Example:**
```python
CATEGORY_MAPPING = {
    'cafe': 'cafe',
    'coffee_shop': 'cafe',
    'restaurant': 'restaurant',
    'fast_food': 'restaurant',
    'shop': 'retail',
    'supermarket': 'retail',
    'bank': 'finance',
    'hospital': 'healthcare',
    # ...
}
```

---

### 5.3 `clustering.py` - Clustering Module

**Purpose:** Groups POIs into clusters to identify business zones.

**Key Class: `GeoClusterer`**

**Algorithms Used:**

#### DBSCAN (Density-Based Spatial Clustering)
- **Why DBSCAN?** It doesn't require specifying number of clusters and can detect noise
- **Distance metric:** Haversine (accounts for Earth's curvature)
- **Parameters:**
  - `eps_km`: Maximum distance between points in a cluster (default: 0.5 km)
  - `min_samples`: Minimum points to form a cluster (default: 5)

```python
# Example:
clusterer = GeoClusterer()
labels = clusterer.dbscan_clustering(coords, eps_km=0.5, min_samples=5)
# Returns: array([-1, 0, 0, 1, 1, 1, -1, 2, ...])
# -1 = noise, 0/1/2/... = cluster IDs
```

#### K-Means (Alternative)
- Used for comparison and optimal cluster count estimation
- Uses silhouette score to find optimal K

#### Hotspot Detection
- Identifies clusters with density above 75th percentile
- High-density areas = high activity zones

#### Sparse Region Detection
- Divides area into grid
- Identifies cells with POI count below 25th percentile
- Sparse areas = potential opportunities

---

### 5.4 `visualize.py` - Visualization Module

**Purpose:** Creates interactive and static maps.

**Key Class: `MapVisualizer`**

**Maps Created:**

1. **All POIs Map** (`all_pois_map.html`)
   - Shows all POIs with marker clustering
   - Color-coded by category
   - Includes legend

2. **Cluster Map** (`cluster_map.html`)
   - POIs colored by cluster assignment
   - Cluster center labels
   - Layer toggle for each cluster

3. **Heatmap** (`heatmap.html`)
   - POI density visualization
   - Gradient: blue (low) → red (high)

4. **Competition Heatmap** (`competition_heatmap.html`)
   - Shows competitor concentration
   - Red markers for competitors

5. **Recommendations Map** (`recommendations_map.html`)
   - Top 10 recommended locations
   - Gold (rank 1-3) / Green (rank 4-10) markers
   - Popup with detailed scores

**Static PNG Exports:**
- `all_pois_map.png` - Scatter plot of POIs
- `cluster_map.png` - Cluster visualization
- `heatmap.png` - Density heatmap (2D histogram)

---

### 5.5 `scoring.py` - Location Scoring Module

**Purpose:** Scores candidate locations using multi-factor model.

**Key Class: `LocationScorer`**

**The Scoring Formula:**
```
Final Score = w₁ × Demand + w₂ × Competition + w₃ × Accessibility + w₄ × Infrastructure
```

**Default Weights:**
- Demand: 0.4 (40%)
- Competition: 0.3 (30%)
- Accessibility: 0.2 (20%)
- Infrastructure: 0.1 (10%)

**Score Components Explained:**

| Component | What It Measures | How It's Calculated |
|-----------|-----------------|---------------------|
| **Demand** | Customer potential | Count of supporting POIs (shops, malls, restaurants) within 1km, normalized 0-1 |
| **Competition** | Competitor presence | INVERSE of competitor count within 0.5km. Fewer competitors = higher score |
| **Accessibility** | Transport access | Count of bus stations, parking, fuel stations nearby |
| **Infrastructure** | Essential services | Count of hospitals, banks, schools, pharmacies nearby |

**Candidate Generation:**
- Creates a grid of points within the search radius
- Filters points outside the circular search area
- Default: 15×15 grid = 225 candidate points (after filtering ~177)

**Process:**
1. Generate candidate grid points
2. For each candidate, use KD-Tree to efficiently count nearby POIs
3. Compute all four scores
4. Apply weights and sum
5. Rank by final score
6. Return top N locations

---

### 5.6 `recommendations.py` - Business Recommendations

**Purpose:** Suggests which business types are viable at a location.

**Key Functions:**

#### `compute_business_recommendations()`
- Evaluates 10 business types: cafe, restaurant, bakery, fast_food, shop, supermarket, pharmacy, bank, gym, hotel
- For each type, calculates:
  - Supporting POI density
  - Competitor density
  - Transport density
  - Infrastructure density
- Returns ranked list of business types by viability

#### `annotate_clusters_with_transport()`
- For each cluster center, finds nearest public transport
- Uses keywords: train, bus, station, footway, footpath
- Returns: transport type, name, and distance in km

---

### 5.7 `report_generator.py` - PDF Report Generation

**Purpose:** Creates professional PDF reports using ReportLab.

**Report Sections:**
1. **Title Page** - City, business type, date
2. **Executive Summary** - Key findings
3. **Methodology** - Scoring weights and algorithms
4. **Analysis Results** - Cluster statistics, hotspots
5. **Recommendations** - Top 10 locations with scores
6. **Conclusion** - Actionable insights

---

### 5.8 `business_suggestion.py` - Business Viability Analysis

**Purpose:** Analyzes which business types suit a specific location.

**Key Class: `BusinessSuggester`**

**Functions:**
- `suggest_businesses_at_location()` - Ranks 10 business types
- `optimize_location()` - Hill-climbing optimization to find optimal coordinates
- `predict_counterfactual_effect()` - "What if" analysis for opening a business

---

### 5.9 `simulation.py` - Agent-Based Simulation

**Purpose:** Simulates customer behavior to predict business viability.

**Key Components:**
- **Agent class:** Represents a customer with income and preferences
- **`run_simulation()`:** Runs N steps of simulation with agents visiting businesses
- **`generate_agents()`:** Creates population with income distribution (normal/uniform/skewed)

---

## 6. Data Flow Explained

### Input Data (from OpenStreetMap):
```csv
name,category,latitude,longitude,source
Starbucks,cafe,12.9716,77.5946,osm
Hotel Marriott,hotel,12.9718,77.5950,osm
SBI Bank,bank,12.9720,77.5955,osm
...
```

### After Cleaning:
```csv
name,category,category_group,latitude,longitude
Starbucks,cafe,cafe,12.9716,77.5946
Hotel Marriott,hotel,hospitality,12.9718,77.5950
SBI Bank,bank,finance,12.9720,77.5955
...
```

### After Clustering:
```csv
name,category,latitude,longitude,cluster_label,is_hotspot
Starbucks,cafe,12.9716,77.5946,0,True
Hotel Marriott,hotel,12.9718,77.5950,0,True
SBI Bank,bank,12.9720,77.5955,1,False
...
```

### Location Scores Output:
```csv
latitude,longitude,demand_score,competition_score,accessibility_score,infrastructure_score,final_score,rank
12.9716,77.5946,0.85,0.72,0.60,0.55,0.732,1
12.9720,77.5950,0.80,0.65,0.70,0.50,0.698,2
...
```

---

## 7. Algorithms & Scoring Logic

### 7.1 Haversine Distance
Used to calculate distance between two lat/lon coordinates accounting for Earth's curvature:

```python
def haversine_distance(point1, point2):
    lat1, lon1 = radians(point1)
    lat2, lon2 = radians(point2)
    
    dlat = lat2 - lat1
    dlon = lon2 - lon1
    
    a = sin(dlat/2)² + cos(lat1) * cos(lat2) * sin(dlon/2)²
    c = 2 * arcsin(sqrt(a))
    
    return EARTH_RADIUS_KM * c  # 6371.0 km
```

### 7.2 DBSCAN Parameters
```python
eps_km = min(radius_km / 10, 0.5)  # Max 0.5km between cluster points
min_samples = max(3, int(len(coords) / 100))  # At least 3, or 1% of data
```

### 7.3 KD-Tree for Spatial Queries
Used for efficient nearest-neighbor searches:
```python
from scipy.spatial import cKDTree
tree = cKDTree(poi_coords)
radius_deg = radius_km / 111.0  # Convert km to degrees
nearby_indices = tree.query_ball_point(candidate_coord, radius_deg)
count = len(nearby_indices)
```

---

## 8. Running the Application

### Option 1: Streamlit Dashboard (Recommended)
```bash
# Install dependencies
pip install .
# or
pip install osmnx geopandas folium scikit-learn pandas numpy shapely streamlit streamlit-folium scipy matplotlib seaborn reportlab

# Run the dashboard
streamlit run app.py --server.port 5000
```
Then open: `http://localhost:5000`

### Option 2: Command Line Interface
```bash
# Interactive mode
python main.py --interactive

# Direct execution
python main.py --city "Bangalore" --business "cafe" --radius 5
```

### CLI Arguments:
| Argument | Short | Description |
|----------|-------|-------------|
| `--city` | `-c` | City name to analyze |
| `--business` | `-b` | Business type (cafe, restaurant, shop, etc.) |
| `--radius` | `-r` | Search radius in km (default: 5) |
| `--interactive` | `-i` | Run in interactive mode |

---

## 9. Output Files Explained

### Data Files (`/data/`):

| File | Contents |
|------|----------|
| `raw_data.csv` | Original POIs fetched from OpenStreetMap |
| `cleaned_data.csv` | Processed POIs with normalized categories |
| `clusters.csv` | POIs with cluster assignments and hotspot flags |
| `location_scores.csv` | Ranked candidate locations with all scores |
| `analysis_report.pdf` | Generated PDF report |

### Map Files (`/maps/`):

| File | Purpose |
|------|---------|
| `all_pois_map.html` | Interactive map of all POIs |
| `all_pois_map.png` | Static PNG version |
| `cluster_map.html` | Cluster visualization |
| `cluster_map.png` | Static PNG version |
| `heatmap.html` | POI density heatmap |
| `heatmap.png` | Static PNG version |
| `competition_heatmap.html` | Competitor density |
| `recommendations_map.html` | Top recommended locations |

---

## 10. Dashboard Features

### Sidebar (Configuration):
- **Location Input:** City name or coordinates (lat/lon)
- **Business Type:** Dropdown with 10+ options + "Best Recommendation"
- **Search Radius:** 1-20 km slider
- **Scoring Weights:** 4 sliders for demand/competition/accessibility/infrastructure

### Tabs:

#### 📍 POI Map
- Shows all fetched POIs
- Download HTML/PNG options

#### 🔵 Clusters
- Cluster visualization map
- Statistics: cluster count, sizes, densities
- Nearest transport info for top clusters

#### 🔥 Heatmaps
- Toggle between POI density and competition heatmaps
- Download options

#### ⭐ Recommendations
- Interactive map with ranked locations
- Top 10 locations list with expandable details
- For "Best Recommendation" mode: shows top 3 business types

#### 🎯 Scenario Modeling
- Create what-if scenarios with different weights
- Save and compare scenarios
- Quick re-score without refetching data

#### 🌍 Multi-City Comparison
- Add multiple cities for side-by-side comparison
- Chart visualization (Altair)
- Best city recommendation

#### 📊 Reports
- Download CSV files
- Generate PDF report
- Analysis summary

### Advanced Features Tab:
- **Opportunity Scoring:** Custom weighted score calculator
- **KDE Heatmap:** Kernel density estimation visualization
- **Agent Simulation:** Run customer behavior simulation
- **Counterfactual Analysis:** Predict impact of opening a business

---

## 11. Dependencies

Listed in `pyproject.toml`:

| Package | Purpose |
|---------|---------|
| `osmnx` | OpenStreetMap data fetching |
| `geopandas` | Geospatial data manipulation |
| `folium` | Interactive map creation |
| `streamlit-folium` | Folium integration with Streamlit |
| `scikit-learn` | Clustering algorithms (DBSCAN, KMeans) |
| `pandas` | Data manipulation |
| `numpy` | Numerical operations |
| `scipy` | Spatial algorithms (KD-Tree) |
| `shapely` | Geometric operations |
| `matplotlib` | Static visualizations |
| `seaborn` | Enhanced visualizations |
| `reportlab` | PDF generation |
| `streamlit` | Web dashboard framework |
| `requests` | HTTP requests |

---

## 12. Configuration

### `.streamlit/config.toml`:
```toml
[server]
headless = true
address = "0.0.0.0"
port = 5000
```

### OSMnx Cache:
- Location: `cache/osmnx/`
- Automatic caching enabled for repeated queries
- Bulk fetch cache: `cache/bulk/`

---

## Quick Reference Summary

### The Core Question:
**"Where is the best location to open a [business_type] in [city]?"**

### The Answer Process:
1. Fetch all businesses in the area from OpenStreetMap
2. Clean and organize the data
3. Group businesses into clusters
4. For every candidate location, calculate:
   - How many potential customers are nearby? (Demand)
   - How many competitors are nearby? (Competition - lower is better)
   - Is it easy to access? (Accessibility)
   - Are essential services nearby? (Infrastructure)
5. Combine these scores with user-defined weights
6. Rank all locations
7. Show top 10 on a map with detailed scores

### Key Insight:
The "best" location balances:
- **High demand** (many supporting businesses nearby)
- **Low competition** (few existing competitors)
- **Good accessibility** (transport links)
- **Good infrastructure** (banks, hospitals, schools)

---

*This documentation was created to help you understand the BusinessLocationFinder/GeoClusterEngine project from scratch.*
