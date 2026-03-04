## 1. Docker Containers: The "Immutable Box"

A Docker container is a standardized package of software. It includes everything: the OS, libraries, and your code.

* **The Key Insight:** "Immutable" means once the box is built, you don't change it. If you need to update the app, you throw the old box away and build a new one.
* **The Component:** The `Dockerfile`. It’s a text file with a list of instructions on how to assemble the box.

## 2. Dev Containers: The "Standardized Workshop"

While a Docker container is for **running** your app, a **Dev Container** is for **writing** it.

* **The Problem:** You have Node v20, your teammate has v18, and the server has v16. Things break.
* **The Solution:** You define your development environment in a `.devcontainer/devcontainer.json` file. When you open the project in VS Code, it "enters" a container that has the exact extensions, themes, and Node version you pre-selected.
* **Pareto Tip:** This is the #1 way to onboard a new developer in 5 minutes instead of 5 hours.

## 3. Multi-Stage Builds: The "Lean Machine"

When you build your React dashboard, you need Node.js, `npm`, and hundreds of `devDependencies`. But when you **run** it in production, you only need the static `index.html` and a tiny web server like Nginx.

* **How it works:** You use one "heavy" stage to build the code, then **copy** only the finished results into a "light" second stage.
* **The Result:** Your image size drops from **800MB** (Node + Tools) to **20MB** (just the Dashboard).

## 4. Mounting Drives: The "Persistent Anchor"

Containers are **stateless**. If you save data to a SQLite file *inside* a container and then stop the container, **your data is deleted forever.** To save your sensor data, you must "mount" a drive (Volume or Bind Mount).

* **Bind Mount:** Maps a folder on your laptop directly to a folder in the container (Great for "hot-reloading" code while you type).
* **Volume:** A dedicated storage area managed by Docker (Best for your SQLite database).

## TL;DR

| Feature | Primary Goal | Use Case |
| --- | --- | --- |
| **Docker Image** | Portability | Sending your app to the Cloud/Server. |
| **Dev Container** | Consistency | Ensuring every dev has the same VS Code setup. |
| **Multi-Stage** | Efficiency | Keeping production images small and secure. |
| **Volumes** | Persistence | Saving your SQLite DB so it survives restarts. |


## Comparisons

| Feature | Dockerfile | devcontainer.json |
| --- | --- | --- |
| **Focus** | **System Level:** OS, compilers, environment variables, system packages. | **IDE Level:** Editor settings, extensions, UI preferences, lifecycle hooks. |
| **Scope** | Generic to any Docker runner. | Specific to VS Code (or tools following the Dev Container spec). |
| **Automation** | Installs things like `git`, `python`, or `gcc`. | Installs extensions like `ESLint`, sets the theme, or runs a "welcome" script. |
| **Portability** | Used for Dev, CI, and Production. | Used only for Development. |

| Feature | Multi-Stage Build | Mounting Drives (Volumes/Bind Mounts) |
| --- | --- | --- |
| **Primary Goal** | Creating a tiny, secure, self-contained **Production Image**. | Connecting the container to **Host Files** (source code) or **Persistent Data** (databases). |
| **Data Location** | Files are **inside** the image layers. | Files stay **outside** on your laptop or server. |
| **Best For** | Compiling code and stripping out build tools. | "Hot reloading" code during dev or saving database data. |
| **Portability** | High: The image works exactly the same on any machine. | Low: Requires specific folders to exist on the host machine. |
| **Performance** | Fast: Files are read directly from the container's local disk. | Slightly slower: There is an I/O overhead between the host and container. |

# PLAN

Phase 1: Preparation
[ ] Create .dockerignore: Add node_modules, dist, .env, and *.log. This prevents your local junk from slowing down the build.

[ ] Configure Vite: Update vite.config.js to set server: { host: true } so the container can talk to your browser.

Phase 2: The Build
[ ] Write Multi-stage Dockerfiles: Create one for /client (serving static files with Nginx) and one for /server (running the Node process).

[ ] Optimize for Caching: Copy package.json and run npm install before copying your source code. This makes subsequent builds 10x faster.

Phase 3: The Environment
[ ] Setup .devcontainer: Create a devcontainer.json that references a docker-compose.yml.

[ ] Port Mapping: Map port 5173 for the frontend and 3000 for the API.

Phase 4: Persistence
[ ] Configure Compose Volumes: In docker-compose.yml, mount your /server/data folder to a volume named sqlite_storage.

[ ] Test Persistence: Create a user in your dashboard, stop the container (docker-compose down), start it again, and verify the user still exists.
