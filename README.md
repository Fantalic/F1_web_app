# F1 Web App

> An interactive Formula 1 data explorer — from CSV to fullstack web application.

A three-part project that converts Kaggle F1 datasets into a searchable, editable web app. A Go tool ingests CSV files into SQLite, a FastAPI backend serves the data via REST, and a Next.js SPA provides the UI with sortable tables, detail views, and embedded maps.

## Project Structure

```
├── csv-to-sql-go-tool/         # Go CLI: CSV → SQLite converter
│   └── create_db.go
├── fastapi-db/                 # FastAPI backend (Python)
│   ├── src/
│   │   ├── api.py              # REST endpoints (CRUD, filtering, search)
│   │   └── db.py               # SQLite database access layer
│   ├── data/formel1.db         # Generated database
│   ├── requirements.txt
│   ├── run.bat / run.sh
│   └── setup.sh
├── nextjs-app/                 # Next.js frontend (TypeScript)
│   ├── src/
│   │   ├── app/                # Next.js App Router pages
│   │   │   ├── layout.tsx
│   │   │   ├── page.tsx        # Main table view
│   │   │   ├── [...slug]/page.tsx  # Dynamic routing
│   │   │   └── __detail/page.tsx   # Detail view
│   │   ├── components/
│   │   │   ├── _APP.tsx        # Main app shell with URL state
│   │   │   ├── Table.tsx       # Sortable, searchable data table
│   │   │   ├── Details.tsx     # Entry detail with embedded map
│   │   │   ├── SearchBar.tsx
│   │   │   ├── CheckBox.tsx    # Column visibility selector
│   │   │   ├── DropDown.tsx
│   │   │   ├── EntryBtnBar.tsx # CRUD actions
│   │   │   ├── NewEntryModal.tsx
│   │   │   ├── HighlightedText.tsx
│   │   │   └── icons/          # SVG icon components
│   │   ├── _api_calls.ts       # API client
│   │   ├── _types.ts           # TypeScript type definitions
│   │   ├── _globals.ts         # Global state & utilities
│   │   ├── _utils.ts
│   │   └── types.db.ts         # Database schema types
│   ├── next.config.ts          # Dev proxy (CORS workaround)
│   ├── package.json
│   └── tsconfig.json
└── README.md
```

## Architecture

The application is split into three independent components connected via clear interfaces:

```
Kaggle CSV → [Go Tool] → SQLite DB → [FastAPI REST API] → [Next.js SPA] → Browser
```

- **Go CLI tool** — standalone CSV-to-SQLite converter, can be reused for any CSV dataset
- **FastAPI backend** — RESTful API with filtering, sorting, and CRUD operations on the F1 dataset
- **Next.js frontend** — single-page application with URL-based state management, column visibility controls, sorting, inline editing, and embedded Google Maps for location data

The dev environment uses Next.js's built-in proxy to forward `/api/*` requests to the FastAPI backend, avoiding CORS issues.

## Quick Start

### Prerequisites
- Go 1.20+ (for CSV conversion)
- Python 3.10+
- Node.js 18+

### Setup

```bash
git clone <repo>
cd F1_web_app

# 1. Generate database
# Download F1 data from Kaggle (see Sources), then:
cd csv-to-sql-go-tool
go build -o create_db.exe
./create_db.exe <csv-folder> formel1.db
copy formel1.db ..\fastapi-db\data\
cd ..

# 2. Backend
cd fastapi-db
python -m venv .venv
.venv\Scripts\activate
pip install -r requirements.txt
cd ..

# 3. Frontend
cd nextjs-app
npm install
cd ..
```

### Run

```bash
# Terminal 1: Backend
cd fastapi-db
.venv\Scripts\activate
python src/api.py

# Terminal 2: Frontend
cd nextjs-app
npm run dev
```

Then open `http://localhost:3000/`.

## Features

- **Search & sort** — dynamic filtering across all columns, sort by any field
- **Column visibility** — show/hide columns via checkboxes
- **Inline editing** — edit values directly in the table, persist to backend
- **URL-based state** — table state (sort, filters, selection) is stored in the URL, shareable and bookmarkable
- **Detail view** — full entry details with embedded Google Maps for location data
- **Auto-scroll** — selection follows the active entry
- **Wiki image lookup** — auto-fetches driver/constructor images from Wikipedia

## Technologies

- **Go** — CSV-to-SQLite converter, standalone CLI
- **Python / FastAPI** — REST API server
- **TypeScript / Next.js** — Frontend SPA with App Router
- **SQLite** — Embedded database
- **Tailwind CSS** — Utility-first styling
- **Google Maps API** — Location visualization

## Sources

- Formula 1 dataset: [Kaggle — Formula 1 World Championship (1950-2020)](https://www.kaggle.com/datasets/rohanrao/formula-1-world-championship-1950-2020)
