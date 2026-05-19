# CP3402 SupA

# Project Overview

This project is a custom WordPress website developed for U3A using a block-based Full Site Editing (FSE) workflow. The site uses a custom child theme built on top of the Neve FSE parent theme and is developed locally using Docker before being deployed to production.

## Deployment

## Theme Development

1. AWS hosting instance
2. how changes are made and committed
   1. create branch
   2. make changes local
   3. test changes
   4. stage changes 
   5. commit changes
   6. push to remote
3. how changes are tested
   1. local testing
   2. them-specific testing
   3. functional testing
   4. CI testing
4. local to production
   1. trigger deployment (via PR to main branch from development branch)
   2. workflow details
   3. required secrets
   4. access local site 
   5. manual migratation from local to production

# 1.0 Install Required Tools

## 1.1 Local Tools 

Install the latest version of docker desktop: [Download Here](https://www.docker.com/products/docker-desktop/)

Install the latest version of Git: [Download Here](https://git-scm.com/install/)

# 2.0 Clone Repo to Local Machine

Open a terminal on the local machine.

Clone the repository:

``` Bash
git clone https://github.com/Jack-Lean-1404/CP3402-SupA.git
```

Move into the project directory:

``` Bash
cd CP3402-SupA
```

# 3.0 Starting the Local WordPress Environment
The project runs inside a Docker container. Ensure when you run the following commands you do so inside the ```CP3402-SupA``` folder.

Start the enviroment:

``` Bash
docker compose up -d
```

Stop the enviroment:

``` Bash
docker compose down
```

View running containers:

``` Bash
docker ps
```

# 4.0 Enviroment Configuration

The repository already includes the required local development environment configuration.

No additional environment variable setup is required for standard local development.

If environment variables need to be changed in future, they are stored in:

```docker-compose.local.yml ```

# 5.0 Local Wordpress Site Setup
Ensure containers are running before opening local site.

Local Site: [localhost:8080](localhost:8080)

Local Site Admin: [localhost:8080/wp-admin/](localhost:8080/wp-admin/)

## 5.1 Activate the project theme

Inside WordPress Admin:

```Appearance -> Themes```

Activate:

```neve-fse-chile```

This is CP3402-SupA's custom child theme

# 6.0 AWS Production Hosting

The production deployment environment for the WordPress website was hosted using Amazon Web Services (AWS) Elastic Compute Cloud (EC2). The production server was configured to mirror the local Docker development environment to ensure consistency between development and deployment.

---

## 6.1 Production Environment Overview

The production environment consisted of:

- AWS EC2 Ubuntu Server instance
- Docker and Docker Compose
- WordPress container
- MariaDB container
- GitHub repository integration
- Automated deployment workflow using GitHub Actions

This environment allowed the website theme code to be automatically deployed from GitHub to the live production server whenever updates were pushed to the main branch.

---

## 6.2 EC2 Instance Configuration

An EC2 instance was created through the AWS Management Console using the following configuration:

| Setting | Configuration |
|---|---|
| Operating System | Ubuntu Server 26.04 LTS |
| Instance Type | t2.micro |
| Hosting Platform | AWS EC2 |
| Region | ap-southeast-2 (Sydney) |
| Access Method | SSH |
| Web Server Access | HTTP Port 80 / 8080 |

A security group was configured to allow inbound traffic for:

| Protocol | Port | Purpose |
|---|---|---|
| SSH | 22 | Remote server administration |
| HTTP | 80 | Web traffic |
| Custom TCP | 8080 | Docker WordPress container |

---

## 6.3 Docker Production Deployment

Docker was installed on the EC2 instance to host the production WordPress environment.

The production server used the same `docker-compose.yml` configuration as the local development environment to ensure deployment consistency.

The following services were deployed:

- WordPress
- MariaDB

The Docker Compose configuration included:

- Persistent MariaDB database volumes
- Mounted child theme directory
- PHP configuration mounting
- Container restart policies

Example Docker volume configuration:

```yaml
volumes:
  - db_data:/var/lib/mysql
```

This ensured WordPress database data persisted between container restarts and deployments.

---

## 6.4 Production Theme Deployment

The WordPress child theme was stored within the GitHub repository and mounted into the WordPress Docker container using Docker volumes.

Example volume mount:

```yaml
volumes:
  - ./wp-content/themes:/var/www/html/wp-content/themes
```

This allowed:

- Local development through Visual Studio Code
- Git version control tracking
- Automatic synchronization between GitHub and production
- Persistent theme files outside the container filesystem

---

## 6.5 Automated Deployment Workflow

A Continuous Deployment (CD) workflow was implemented using GitHub Actions.

The workflow automatically deployed changes to the production server whenever commits were pushed to the `main` branch.

Deployment workflow process:

```text
Local Development
        ↓
Git Commit
        ↓
Git Push to GitHub
        ↓
GitHub Actions Workflow Trigger
        ↓
SSH Connection to AWS EC2
        ↓
git pull on Production Server
        ↓
Docker Container Restart
        ↓
Production Website Updated
```

---

## 6.6 SSH Authentication

SSH key authentication was configured between GitHub Actions and the AWS EC2 server.

The deployment workflow used:

- Public SSH key stored on the EC2 server
- Private SSH key stored securely in GitHub Repository Secrets

The following repository secrets were configured:

| Secret Name | Purpose |
|---|---|
| `EC2_HOST` | Production server public IP |
| `EC2_USERNAME` | SSH login username |
| `EC2_SSH_KEY` | Private SSH deployment key |

---

## 6.7 GitHub Actions Workflow Configuration

A GitHub Actions workflow file was created within:

```text
.github/workflows/deploy.yml
```

The workflow automatically connected to the EC2 server and updated the production deployment.

Example workflow configuration:

```yaml
name: Deploy WordPress Theme

on:
  push:
    branches:
      - main

jobs:
  deploy:
    runs-on: ubuntu-latest

    steps:
      - name: Deploy to AWS
        uses: appleboy/ssh-action@master

        with:
          host: ${{ secrets.EC2_HOST }}
          username: ${{ secrets.EC2_USERNAME }}
          key: ${{ secrets.EC2_SSH_KEY }}

          script: |
            cd ~/CP3402-SupA
            git pull
            sudo docker compose restart
```

---

## 6.8 Deployment Outcome

The final deployment workflow successfully achieved:

- Automated production deployment
- Cloud-hosted WordPress environment
- Persistent Docker-based hosting
- Version-controlled child theme deployment
- GitHub-integrated CI/CD workflow
- Reproducible production infrastructure

This deployment process ensured the production server remained synchronized with the latest approved theme changes from the GitHub repository.
