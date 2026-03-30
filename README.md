# The Office — Organizational Chart

## Overview

A fun, interactive organizational chart visualizing the hierarchy of the Dunder Mifflin Scranton branch from the beloved TV show, "The Office".

## Technical Highlights

- **SQLAlchemy (ORM)**: Models the employee hierarchy across three tiers — employees, managers, and executives. Handles complex queries and manages database sessions through custom context managers and decorators.
- **Pydantic (Data Validation)**: All input is validated before it touches the database. This is the same pattern used in ML model serving to catch bad data at the boundary.
- **PostgreSQL**: Stores and queries the organizational structure. The app connects through a scoped, non-superuser role following least-privilege principles.
- **Docker and Docker Compose**: The app and database run as containerized services, making the environment consistent and easy to spin up.
- **NetworkX (Graph Theory)**: Builds a directed graph from SQL query results to represent the reporting hierarchy — the same kind of structure used in dependency graphs and graph neural networks.
- **Streamlit**: Powers the multi-page frontend without any HTML or JavaScript boilerplate.

## Features

- **Data Pipeline**: `build.py` includes a `rebuild` command that clears and reconstructs the database in the correct dependency order using bulk inserts.
- **Interactive Org Chart**: Browse the full chain of command across all three tiers through a clean multi-page Streamlit interface.
- **Error Handling**: Database errors, session rollbacks, and exception definitions are all centralized and managed consistently across the app.
- **Tests**: Covers validation logic, ID generation, and UI components using pytest, pytest-mock, and pytest-cov.

## Project Structure

```
theoffice-orgchart/
├── app.py                    # Streamlit entry point
├── build.py                  # Data pipeline
├── docker-compose.yml        # Service orchestration
├── pyproject.toml            # Dependencies (uv)
├── config/                   # Environment and database configuration
├── handler/
│   └── cursor.py             # Session and cursor management
├── models/
│   └── orgchart.py           # SQLAlchemy ORM models
├── pages/
│   ├── Home.py               # Landing page
│   └── ChainOfCommand.py     # Chain-of-command explorer
├── utilities/
│   ├── connection_helper.py  # Database connection factory
│   ├── errors.py             # Exception definitions
│   ├── graph_builder.py      # NetworkX graph construction
│   ├── id_generator.py       # ID generation logic
│   ├── session_helper.py     # Session context manager
│   └── validation.py         # Pydantic input validation
└── tests/
    ├── conftest.py
    ├── test_id_generator.py
    ├── test_ui_components.py
    └── test_validation.py
```

## Getting Started

### Prerequisites

- [Docker](https://www.docker.com/) and Docker Compose
- Python 3.11+ and [uv](https://docs.astral.sh/uv/)

### Installation

1. Clone the repository:
   ```bash
   git clone https://github.com/your-username/theoffice-orgchart.git
   cd theoffice-orgchart
   ```

2. Sync the dependencies:
   ```bash
   uv sync
   ```

3. Copy the environment template and fill in your database credentials:
   ```bash
   cp template.env .env
   ```

   | Variable | Description |
   |---|---|
   | `PG_USER` | PostgreSQL superuser username |
   | `PG_PASSWORD` | PostgreSQL superuser password |
   | `PG_DATABASE` | Database name |
   | `PG_PORT` | Host port mapped to container port `5432` |
   | `DB_HOST` | Database hostname (typically `localhost`) |
   | `DB_USER` | Application-level database role |
   | `DB_USER_PW` | Password for the application role |

4. Start the database container:
   ```bash
   docker compose up -d
   ```

   > **Note:** The Docker Compose network setup is currently being refined. Start the Streamlit app manually in a separate terminal for now.

5. In another terminal, run the build pipeline to initialize the database:
   ```bash
   uv run python build.py rebuild
   ```

6. Start the app:
   ```bash
   uv run streamlit run app.py
   ```

7. Visit `http://localhost:8501` in your browser.

## Running Tests

```bash
uv run pytest tests/ -v --cov
```

## License

This project is licensed under the [MIT License](LICENSE).