# Docker & Container Security — Practical Assessment

## Student details

- **Name:** Krishna Mani Tripathi
- **Registration number:** `202401100200177`
- **Port:** `157` (80 + last two digits, 77)
- **Docker Hub username:** `krishnamani12`
- **GitHub repository:** `https://github.com/YOUR_GITHUB_USERNAME/202401100200177-docker-assessment`
- **Docker Hub image:** `krishnamani12/fest-backend`

> Replace `YOUR_GITHUB_USERNAME` with your actual GitHub username. Capture your own assessment screenshots from your machine. This repository intentionally does not contain fabricated screenshots or fake scan/signing evidence.

## Project overview

FestPass is a three-tier registration application with an Nginx frontend, Python/Flask backend, and PostgreSQL database. Docker Compose runs the services on two networks so the frontend is not attached to the database network. PostgreSQL uses a named volume for persistent registration data.

## Repository structure

```text
.
├── README.md
├── compose.yaml
├── .env.example
├── .gitignore
├── backend/
│   ├── app.py
│   ├── requirements.txt
│   ├── Dockerfile
│   └── .dockerignore
├── frontend/
│   ├── index.html
│   └── nginx.conf
├── reports/
└── screenshots/
```

## Prerequisites

Install and start Docker Desktop, Git, Trivy, and Cosign. Authenticate to Docker Hub with `docker login`. Confirm that `docker version` shows both Client and Server sections. Check your installed Trivy version against the assessment warning: do not use versions `0.69.4`, `0.69.5`, or `0.69.6`.

## Git Bash setup

Open Git Bash in the repository root and set the variables for the current terminal session:

```bash
export REGNO="202401100200177"
export PORT="157"
export DOCKERHUB_USERNAME="krishnamani12"
export PS1="[$REGNO] \\w \\$ "
```

The port is calculated as `80 + 77 = 157`.

Create a local `.env` file (it is ignored by Git) with your own local database password:

```dotenv
REGNO=202401100200177
PORT=157
POSTGRES_PASSWORD=replace_with_a_strong_local_password
```

Do not commit `.env`, passwords, access tokens, or `cosign.key`.

## Run the application locally

After creating `.env`, validate and start the services:

```bash
docker compose config
docker compose up -d --build
docker compose ps
curl.exe http://localhost:157/api/health
```

Open `http://localhost:157` in your browser and submit two test registrations. Check logs with:

```bash
docker compose logs backend
docker compose logs db
```

Test persistence without deleting the volume:

```bash
docker compose down
docker compose up -d
docker compose ps
docker volume ls
```

Do **not** use `docker compose down -v` when testing persistence.

## Assessment stages and evidence

Follow the supplied assessment PDF and commit after each stage. Use the historical starter requirements/base image for Stage 1, then use the hardened Dockerfile and upgraded pinned dependencies in Stage 3. Keep screenshots authentic: take full-window screenshots on your own machine, showing your registration-number prompt in terminal screenshots. Do not create or submit mock screenshots as if they were command output.

| Evidence file | Capture yourself |
|---|---|
| `screenshots/S1-2.png` | Stage 1 build and image size |
| `screenshots/S1-3.png` | Stage 1 `docker ps`, health response, logs |
| `screenshots/S2-2.png` | Frontend cannot reach database |
| `screenshots/S2-3.png` | Browser showing REGNO and two registrations |
| `screenshots/S2-4.png` | Registrations persist after restart; named volume |
| `screenshots/S3-1.png` | Trivy report for original image |
| `screenshots/S3-3.png` | Fixed-image Trivy check and exit code |
| `screenshots/S4-1.png` | Docker Hub push output and digest |
| `screenshots/S4-2.png` | Docker Hub Tags page |
| `screenshots/S4-3.png` | Published image `1.0.0` running with saved registrations |
| `screenshots/S5-1.png` | Cosign key generation (do not expose key/password) |
| `screenshots/S5-2.png` | Signing `1.0.0` by digest |
| `screenshots/S5-3.png` | Successful verification |
| `screenshots/S5-4.png` | Failed verification of unsigned `1.0.1` |

Save the actual images in `screenshots/`. Do not reuse or edit other people's evidence.

## Vulnerability results

Fill in this table from your own Trivy output. Do not guess counts or claim zero vulnerabilities unless your reports show that result.

| Metric | `fest-backend:0.1` (before) | `fest-backend:0.2` (after) |
|---|---:|---:|
| CRITICAL vulnerabilities | To be measured | To be measured |
| HIGH vulnerabilities | To be measured | To be measured |
| Image size | To be measured | To be measured |

Required reports:

- `reports/trivy-before.txt`
- `reports/trivy-after.txt`

Stage 3 required check:

```bash
trivy image --severity CRITICAL --ignore-unfixed --exit-code 1 fest-backend:0.2
echo $?
```

Expected exit code is `0` when no CRITICAL vulnerabilities with available fixes are detected. The check does not establish that the image has no vulnerabilities of any severity.

## Publish and sign

Use your own Docker Hub repository:

```text
krishnamani12/fest-backend
```

Build with the application version:

```bash
docker build --build-arg APP_VERSION=1.0.0 -t fest-backend:release ./backend
docker tag fest-backend:release krishnamani12/fest-backend:1.0.0
docker tag fest-backend:release krishnamani12/fest-backend:1.0
docker push krishnamani12/fest-backend:1.0.0
docker push krishnamani12/fest-backend:1.0
```

Record the actual `1.0.0` digest reported by Docker Hub in this README after pushing:

```text
sha256:REPLACE_WITH_ACTUAL_DIGEST
```

Generate a password-protected Cosign key pair with `cosign generate-key-pair`. Commit only `cosign.pub`; never commit `cosign.key`. Sign and verify `1.0.0` by its **digest**, then publish `1.0.1` without signing and demonstrate that verification fails. Do not copy a digest from another image or account.

## AI tools used

ChatGPT was used to help interpret the task and draft the starter configuration. Update this disclosure to accurately describe the tools you actually use.

## Incomplete tasks / results

Complete this section honestly with any steps you could not finish. Remove this instruction only after documenting the actual outcome.
