> University project - DAVE3606 at OsloMet (2026)
# LEGO Inventory System - DAVE3606

A full-stack web application for browsing and querying LEGO set data, built with Python and PostgreSQL. The project focuses on backend performance, efficient data serialization, and clean API design.

## Tech Stack

- **Backend:** Python (Flask)
- **Database:** PostgreSQL
- **Frontend:** HTML, JavaScript (vanilla)
- **Testing:** pytest

## Features

### Database & Querying
- Relational data model with primary and foreign keys across `lego_set`, `lego_brick`, and `lego_inventory` tables
- Composite primary keys to handle many-to-many relationships between brick types and colors
- Parameterized SQL queries to prevent SQL injection
- Index analysis: measured query performance before and after indexing, and documented when indexes hurt rather than help (low-selectivity queries)

### Performance Optimization
- Identified O(n²) bottleneck caused by string concatenation inside a loop
- Refactored to O(n) using `"".join(list)` - reduced response time from **3.7s to 0.4s**
- Server-side LRU cache using `OrderedDict` storing the 100 most recently accessed sets
- `Cache-Control` HTTP headers for client-side caching (60s TTL)

### API & Serialization
- REST API endpoint `/api/set` returning structured JSON with full inventory data
- Custom binary serialization format (`/api/set_binary`) using `struct.pack` for integers and length-prefixed UTF-8 strings
- Encoding support via query parameters (UTF-8 / UTF-16) with gzip compression
- Companion CLI reader (`read_set_binary.py`) to deserialize and display binary files in terminal

### Architecture & Testing
- Refactored endpoints using dependency injection (`Database` class over psycopg)
- Logic separated from request handling to enable unit testing without a live database
- Mock database in tests to verify SQL correctness and HTML/JSON output
- All tests passing with pytest

## What We Learned

Indexes don't always improve performance - when a query matches a large portion of the table, a sequential scan can be faster than an index scan. Measuring before and after is the only way to know.

String concatenation in loops is a classic O(n²) trap in Python. Switching to list accumulation and `.join()` at the end is the idiomatic fix and had a dramatic effect here.

## Setup

```bash
# Clone the repo
git clone https://github.com/SyaDow017/lego-inventory-system.git
cd lego-inventory-system

# Install dependencies
pip install -r requirements.txt

# Start the database
bash create_and_run_database.sh

# Import data
python import_into_database.py

# Run the server
python server.py
```

Visit `http://localhost:5000/sets` to browse all LEGO sets.

## Running Tests

```bash
python -m pytest
```

## Authors

- Hanifah Sandow Salifu
- Samira Ali Ahmed Elmi
