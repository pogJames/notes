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
