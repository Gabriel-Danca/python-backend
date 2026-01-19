For this backend, we are going to use the following tools:
- FastAPI 
- PostgreSQL
- Poetry
- SQLAlchemy + psycopg 
- Uvicorn / Gunicorn
- Docker / Docker compose

The following commands were verified on a Linux system as of January 19, 2026. Please note that tools and best practices evolve over time. If you encounter issues, refer to the official documentation for each tool (links provided) to adapt to any changes.

Poetry: https://python-poetry.org/docs/


If you don't have Poetry installed, please run the following command:

```bash
sudo apt install python3-poetry
```

First, we are going to create a new project: 

```bash
poetry new python-backend
```

Next, change to the project directory:

```bash
cd python-backend/
```

Install all necessary dependencies for the project:

```bash
poetry add gunicorn alembic fastapi "uvicorn[standard]" sqlalchemy psycopg pydantic-settings python-dotenv
```

To run the database, run the following:
docker compose up -d

Initialize Alembic:
poetry run alembic init alembic

// replace

Alembic init + conectare la metadata

Inițializează Alembic:

poetry run alembic init alembic


În alembic/env.py, setează target_metadata pe Base.metadata și importă modelele:

Caută target_metadata = None și înlocuiește cu:

from app.db.base import Base
from app.models import Todo  # doar ca să fie încărcat modelul

target_metadata = Base.metadata


În alembic.ini, la sqlalchemy.url, pune URL-ul tău (ca să meargă imediat). Exemplu:

sqlalchemy.url = postgresql+psycopg://app:PAROLA@localhost:5432/appdb


Generează migrarea:

poetry run alembic revision --autogenerate -m "create todos table"


Aplică migrarea:

poetry run alembic upgrade head


După asta, tabela există.

To run the server, use the following:
poetry run uvicorn app.main:app --reload
poetry run gunicorn -k uvicorn.workers.UvicornWorker -w 1 -b 127.0.0.1:8000 app.main:app

///

```bash
mkdir -p app
touch app/app.py
```

Paste the following code in the app.py file:

```python
from fastapi import FastAPI

app = FastAPI(title="fastapi-backend")

@app.get("/health")
def health():
    return {"status": "ok"}
```

Am acest fisier de docker-compose:
services:
  db:
    image: postgres:18-alpine3.23
    container_name: python-backend-db
    environment:
      POSTGRES_USER: user
      POSTGRES_PASSWORD: pass98
      POSTGRES_DB: appdb
    ports:
      - "5432:5432"
    volumes:
      - pgdata:/var/lib/postgresql/

volumes:
  pgdata:

cat > .env << 'EOF'
DATABASE_URL=postgresql+psycopg://app:app@localhost:5432/appdb
EOF


mkdir -p app/core app/db
touch app/core/__init__.py app/db/__init__.py

app/core/config.py
from pydantic_settings import BaseSettings

class Settings(BaseSettings):
    database_url: str

    class Config:
        env_file = ".env"

settings = Settings()

poetry run alembic init alembic