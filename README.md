# 🐘 Ryan's Basic AF PG Stack

This project provides a pre-configured PostgreSQL + pgAdmin environment using Docker Compose. It's designed for local development and simplifies managing your database and inspecting it through a user-friendly UI.

---

## 🧱 Stack Overview

| Service        | Description                                                                                          |
| -------------- | ---------------------------------------------------------------------------------------------------- |
| **PostgreSQL** | Relational database (PostgreSQL 15) running in a Docker container.                                   |
| **pgAdmin 4**  | Web-based admin interface for PostgreSQL. Allows you to inspect DB schemas, tables, and run queries. |

---

## 📦 Prerequisites

- [Docker](https://docs.docker.com/get-docker/)
- [Docker Compose](https://docs.docker.com/compose/)
- [Node.js](https://nodejs.org/) (optional, for running NPM-based scripts)

---

## ⚙️ Setup Instructions

### 1. Clone the Repository

```bash
git clone https://github.com/your-username/generic-pg.git
cd generic-pg
```

### 2. Create Your `.env` File

Start by copying the provided `.env` template:

```bash
cp .env.example .env
```

Then open `.env` and fill out the values:

```env
# PostgreSQL
POSTGRES_USER=your_db_user
POSTGRES_PASSWORD=your_db_password
POSTGRES_DB=your_db_name
POSTGRES_CONTAINER_NAME=zander_pg_postgres
POSTGRES_PORT=5432

# pgAdmin
PGADMIN_DEFAULT_EMAIL=your@email.com
PGADMIN_DEFAULT_PASSWORD=your_pgadmin_password
PGADMIN_PORT=8080
```

> 💡 Tip: Use a port like `8080` for `PGADMIN_PORT` to avoid conflict with system services on port 80.

---

### 3. Create the Docker Network

Before starting the stack, create the shared network `pgnet`:

```bash
docker network create pgnet
```

This allows PostgreSQL and pgAdmin to communicate internally.

---

## ▶️ Running the Stack

You can start, stop, and inspect the stack using NPM scripts defined in `package.json`.

### Start the Stack

```bash
npm run start
```

This runs:

```bash
docker compose up -d
```

### Stop the Stack

```bash
npm run stop
```

Stops and removes containers:

```bash
docker compose down
```

### View Logs

```bash
npm run logs
```

Streams logs for both services.

### Restart the Stack

```bash
npm run restart
```

Convenience command: stops, then starts the stack again.

> You can also use the `:dev` versions if you have a `docker-compose.dev.yml` configured.

---

## 🔍 Accessing pgAdmin

Once the stack is running, open your browser and go to:

```
http://localhost:8080
```

_(Or whatever port you set for `PGADMIN_PORT` in your `.env` file)_

### Login with the credentials from your `.env`:

- **Email:** `PGADMIN_DEFAULT_EMAIL`
- **Password:** `PGADMIN_DEFAULT_PASSWORD`

---

### 🔗 Connect pgAdmin to PostgreSQL

Once logged in:

1. Click **"Add New Server"**.
2. In the **General** tab, name your server (e.g. `Local DB`).
3. Switch to the **Connection** tab and enter:

| Field     | Value from `.env`              |
| --------- | ------------------------------ |
| Host name | `postgres` _(container name)_  |
| Port      | `5432` _(or your mapped port)_ |
| Username  | `POSTGRES_USER`                |
| Password  | `POSTGRES_PASSWORD`            |

Click **Save**, and you're in!

---

## 📂 Docker Compose Overview

```yaml
services:
  postgres:
    image: postgres:15
    container_name: ${POSTGRES_CONTAINER_NAME}
    restart: unless-stopped
    env_file: .env
    ports:
      - "${POSTGRES_PORT:-5432}:5432"
    volumes:
      - postgres_data:/var/lib/postgresql/data
    networks:
      - pgnet

  pgadmin:
    image: dpage/pgadmin4
    container_name: pgadmin
    restart: unless-stopped
    env_file:
      - .env
    ports:
      - "${PGADMIN_PORT:-80}:80"
    depends_on:
      - postgres
    volumes:
      - pgadmin_data:/var/lib/pgadmin
    networks:
      - pgnet

volumes:
  postgres_data:
  pgadmin_data:

networks:
  pgnet:
    name: pgnet
    external: false
```

---

## 🔐 Security Notes

- This setup is intended for **local development**.
- Never commit `.env` files with real credentials.
- Avoid exposing pgAdmin or PostgreSQL directly to the public internet.

---

## 🧼 Cleanup

To completely remove everything including volumes:

```bash
docker compose down -v
docker network rm pgnet
```

---

## 👤 Author

**[@codeMonkeyHopeful](https://github.com/codemonkeyhopeful)**
MIT License
