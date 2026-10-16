# Flights dev container

Includes .NET SDK 10, Node.js 24 (with npm), and PostgreSQL 17.
PostgreSQL runs in a separate service and persists data in a Docker named volume.

## Start

1. Install Docker Desktop with Linux containers enabled and the VS Code
   **Dev Containers** extension (`ms-vscode-remote.remote-containers`).
2. From the workspace root in PowerShell, copy the environment template:

   ```powershell
   Copy-Item .devcontainer\.env.example .devcontainer\.env
   ```

3. Edit `.devcontainer/.env` and replace `POSTGRES_PASSWORD` with a unique local
   development password. This file is ignored by Git; do not commit credentials.
4. Run **Dev Containers: Reopen in Container** in VS Code.

The application container waits for PostgreSQL to become healthy. Creation
checks the installed .NET, Node.js, and npm versions.

## Connect to PostgreSQL

From applications running inside the dev container, use:

| Setting | Value |
| --- | --- |
| Host | `postgres` |
| Port | `5432` |
| Database | `flights` |
| Username | `dev` |
| Password | Your `POSTGRES_PASSWORD` value |

The values in `.env` are available as environment variables in the application
container. `PGHOST` and `PGPORT` are also set; applications must build their
connection strings from these settings explicitly.

For example, an Npgsql connection string has this form (replace the password):

```text
Host=postgres;Port=5432;Database=flights;Username=dev;Password=<your-local-password>
```

To open `psql` from a host terminal at the workspace root:

```powershell
docker compose -f .devcontainer\compose.yaml exec postgres psql -U dev -d flights
```

VS Code forwards PostgreSQL to localhost, along with application ports 3000 and
5000. Check the **Ports** panel for the actual local port if a port is already
in use. Bind development web servers to `0.0.0.0` to make them accessible.
The Compose configuration does not publish the database port to the network.

## Lifecycle

Closing the dev container stops both services; rebuilding preserves database data.
PostgreSQL initialization settings in `.env` only apply to an empty data volume.
Changing the password later requires updating the database role password as well.
Do not remove the named volume unless you intend to erase the development database.
