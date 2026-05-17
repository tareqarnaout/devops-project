**Django 4.0.4 Tutorial Series**  
**To run this app:**

1. **Create .env file that contains DB and django secrets see .env.example for reference.**
2. **Then in book-shop folder:**
3. \*\*Run \*\* \*\*docker-compose up -d --build



&#x20; ###Hammam Al Rawabdeh 20230438

&#x20; ###**Tareq Al Arnaout 20221111**

# Phase 2 - CI/CD Pipelines: Book Shop Deployment

## Group Size & Deployment Choice
We completed this project adhering to the **Group of 3** requirements. 
* **Registry:** We utilized AWS ECR to store our container images.
* **Deployment Automation:** We completely automated the `docker-compose.yml` updates on the EC2 server. Our pipelines dynamically inject the newly built Image URIs and configure port mappings using `sed` and inline file generation directly on the server, ensuring zero manual intervention is required.

## Branch Strategy & Pipeline Methodologies

### 1. `dev` Branch (Artifact-First)
* **Philosophy:** Proves we can ship exactly what we built.
* **How it works:** On push, the pipeline packages the source code into a `.tar.gz` artifact and commits it to an `artifacts/` folder for a historical audit trail. The Docker image is then built explicitly from this zipped artifact (not the raw source tree) before being deployed to EC2.

### 2. `test` Branch (Image-First)
* **Philosophy:** Proves we can reliably rebuild from source at any time.
* **How it works:** On push, the pipeline builds a completely fresh artifact, packages it into a Docker image, and pushes it to our private AWS ECR vault. GitHub Actions securely generates an AWS login token and passes it to the EC2 server via SSH. The server generates a test-specific compose file, pulls the fresh image from ECR, and deploys it.

### 3. `prod` Branch (Promotion Only)
* **Philosophy:** Proves we only deploy what has been tested.
* **How it works:** This pipeline is strictly forbidden from running `docker build` or packaging artifacts. It acts purely as a deployment trigger. It reads the `IMAGE_VERSION` from GitHub Repository Variables (the single source of truth) and promotes that exact pre-built image from AWS ECR to the live production server.

## Server Coexistence (Preventing Collisions)
All three environments run simultaneously on a single EC2 instance without port or container collisions. We achieved this through strict isolation:
* **Isolated Directories:** Each environment deploys to its own dedicated folder on the server (`/home/ubuntu/book_shop_deploy`, `/home/ubuntu/test_env`, `/home/ubuntu/prod_env`).
* **Compose Project Names:** We utilized the `-p` flag (`-p dev`, `-p test`, `-p prod`) to group and isolate containers natively in Docker.
* **Port Mapping:** Nginx handles traffic differently for each environment:
  * **Production (`prod`):** Port `80` (Standard HTTP)
  * **Testing (`test`):** Port `8080`
  * **Development (`dev`):** Port `[]` 

## Secrets Hygiene
No sensitive data is hardcoded in the repository. We utilized **GitHub Secrets** for our AWS Keys, Docker access, Database Passwords, and SSH keys. We utilized **GitHub Variables** for non-sensitive data like the EC2 Host IP, Region codes, and our `IMAGE_VERSION` source of truth.
