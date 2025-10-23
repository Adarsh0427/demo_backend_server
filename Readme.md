
# Demo Backend Server — Learning Project

This is a small learning project that demonstrates a simple Node.js + Express backend using Prisma, and shows how to build and run it inside a Docker container. It's intentionally minimal and meant for learning how Dockerfiles and containerization work.

## What this project contains

- A simple Express server in `src/index.ts` that serves static files from `public/`, provides a health endpoint (`GET /`) and a signup endpoint (`POST /`) that creates a `user` via Prisma.
- A `Dockerfile` used to build a container image for the application.

## Build and run with Docker

From the project root (where the `Dockerfile` is), run the following to build the Docker image and start a container:

```bash
# Build the Docker image (tag it as demo-backend-server)
docker build -t demo-backend-server .

# Run the container, mapping host port 3000 to container port 3000
docker run --rm -p 3000:3000 demo-backend-server
```

After the container starts the server will be reachable at <http://localhost:3000>

Notes:

- Use `-d` with `docker run` to run in detached mode: `docker run -d --rm -p 3000:3000 demo-backend-server`.
- If you make changes to source files, rebuild the image with `docker build`.

## Dockerfile (lines explained)


Below are the lines from the `Dockerfile` with short explanations. This `Dockerfile` is simple and intended for learning.

FROM node: 16-alpine

- Base image: uses the official Node.js 16 image on Alpine Linux. Alpine is a small Linux distribution which keeps the image size down.

WORKDIR /app

- Sets the working directory inside the container to `/app`. All subsequent commands run from here.

COPY . .

- Copies the project files from the host into the container's `/app` directory. For production images you would usually copy only the files you need (e.g., `package.json`, `dist/`) to keep the image small.

RUN npm install

- Installs Node.js dependencies listed in `package.json` inside the container.

RUN npm run build

- Runs the project's build script (typically transpiles TypeScript to JavaScript and writes to `dist/`). Ensure your `package.json` has a `build` script.

EXPOSE 3000

- Documents that the container listens on port 3000. This does not map the port to the host by itself; you do that with `docker run -p`.

CMD ["node", "dist/index.js"]

- The default command run when the container starts. It executes the built Node app. If your build outputs to a different path, update this line accordingly.

## Development notes and assumptions

- This project assumes a `build` script in `package.json` that produces `dist/index.js` from the TypeScript source in `src/`. If you haven't set that up yet, add a build step such as `tsc` or use a bundler.
- The `Dockerfile` in this repo is intentionally simple for learning. For production use consider multi-stage builds to avoid copying dev files and to reduce final image size, and add a `.dockerignore` to exclude node_modules, local env files, and build artifacts during `COPY`.
- Prisma may require additional setup (database URL, migrations). If you plan to run Prisma inside the container, ensure environment variables (like DATABASE_URL) are provided to the container via `-e` flags, Docker secrets, or a docker-compose file.

## Example: run with environment variables

```bash
docker run --rm -p 3000:3000 -e DATABASE_URL="postgresql://user:pass@host:5432/db" demo-backend-server
```

## Next steps (learning suggestions)

- Add a `.dockerignore` file to exclude files from the image build (e.g., `node_modules`, `.git`, `tmp`).
- Convert the `Dockerfile` to a multi-stage build to keep the final image minimal.
- Create a `docker-compose.yml` if you want to run the app together with a database for development.

---

If you want, I can also add a `.dockerignore`, a multi-stage `Dockerfile` example, or a `docker-compose.yml` to run the app with a local database. Which would you like next?
