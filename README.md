# Microservices Code Repository

This repository contains a basic microservices setup with two main components:

1. **Node.js Backend API** (`nodeapp/`)
2. **React.js Frontend Application** (`reactapp/`)

Both services are containerized using Docker and integrated with GitHub Actions for CI/CD automation.

## Prerequisites

- Docker
- Node.js (version 20)
- GitHub account with repository access
- DockerHub account for pushing images (optional for local testing)
- Postgres database credentials

## Setup Instructions

### 1. Clone the Repository
```bash
git clone https://github.com/harikrishnapachava/microservices_code_repo.git
cd microservices_code_repo
```

### 2. Install Dependencies
- For `reactapp`:
  ```bash
  cd reactapp
  npm install
  cd ..
  ```
- For `nodeapp`:
  ```bash
  cd nodeapp
  npm install
  cd ..
  ```

### 3. Configure Environment
- Create a `.env` file in `nodeapp/` with the following content, updating the values as needed:
  ```
  DB_USER=postgres
  DB_HOST=localhost
  DB_NAME=mydb
  DB_PASSWORD=yourpassword
  DB_PORT=5432
  ```

### 4. Set Up the Postgres Database
- Run a Postgres container:
  ```bash
  docker run --name postgres-db -e POSTGRES_USER=postgres -e POSTGRES_PASSWORD=yourpassword -e POSTGRES_DB=mydb -p 5432:5432 -v $(pwd)/init.sql:/docker-entrypoint-initdb.d/init.sql -d postgres:latest
  ```
- The `init.sql` script will initialize the database with a sample `items` table.

### 5. Build and Run the Backend (nodeapp)
- Build the Docker image for `nodeapp`:
  ```bash
  docker build -t nodeapp:latest ./nodeapp
  ```
- Run the `nodeapp` container:
  ```bash
  docker run --name nodeapp -p 5000:5000 --env-file nodeapp/.env -d nodeapp:latest
  ```

### 6. Build and Run the Frontend (reactapp)
- Build the Docker image for `reactapp`:
  ```bash
  docker build -t reactapp:latest ./reactapp
  ```
- Run the `reactapp` container:
  ```bash
  docker run --name reactapp -p 3000:3000 -d reactapp:latest
  ```

### 7. Verify the Setup
- Open `http://localhost:3000` in your browser to see the React frontend fetching data from the backend.
- Use a tool like `curl` or Postman to test the API:
  ```bash
  curl http://localhost:5000/api/data
  ```

### 8. CI/CD with GitHub Actions
- The repository includes GitHub Actions workflows (`reactapp_cicd.yml` and `nodeapp_cicd.yml`) in `.github/workflows/`.
- Configure the following secrets in your GitHub repository settings:
  - `DOCKERHUB_USERNAME`: Your DockerHub username
  - `DOCKERHUB_TOKEN`: Your DockerHub access token
  - `TOKEN`: A personal access token for the `k8s_manifests_repo` repository
- Push changes to the `main` branch to trigger the workflows, which will:
  - Run unit tests and linting
  - Build the project
  - Build and push Docker images to DockerHub
  - Update the Helm chart tag in the `k8s_manifests_repo` repository

## Notes
- The React app fetches data from the backend at `http://localhost:5000/api/data`. Update the URL in `App.js` if your backend is hosted elsewhere.
- Ensure the `.env` file in `nodeapp/` matches your Postgres configuration.
- The GitHub Actions workflows assume you have secrets (`DOCKERHUB_USERNAME`, `DOCKERHUB_TOKEN`, `TOKEN`) set in your repository settings.

## Troubleshooting
- If the frontend fails to load, ensure the backend is running and the API endpoint is correct in `reactapp/src/App.js`.
- Check Docker logs if services fail to start:
  ```bash
  docker logs nodeapp
  docker logs reactapp
  ```
- Ensure all environment variables in `.env` match your Postgres setup.

## Contributing
Feel free to submit issues or pull requests. Contact Harikrishna Pachava at pachava.harikrishna@gmail.com for support.

## License
[MIT License](LICENSE)