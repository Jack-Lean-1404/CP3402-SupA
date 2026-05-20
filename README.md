# CP3402 SupA

# Project Overview

This project is a custom WordPress website developed for U3A using a block-based Full Site Editing (FSE) workflow. The site uses a custom child theme built on top of the Neve FSE parent theme and is developed locally using Docker before being deployed to production.

## Deployment

## Theme Development



## 1.0 Install Required Tools

Install the latest version of docker desktop: [Download Here](https://www.docker.com/products/docker-desktop/)

Install the latest version of Git: [Download Here](https://git-scm.com/install/)

## 2.0 Clone Repo to Local Machine

Open a terminal on the local machine.

Clone the repository:

``` Bash
git clone https://github.com/Jack-Lean-1404/CP3402-SupA.git
```

Move into the project directory:

``` Bash
cd CP3402-SupA
```

## 3.0 Starting the Local WordPress Environment
The project runs inside a Docker container. Ensure when you run the following commands you do so inside the ```CP3402-SupA``` folder.

Start the environment:

``` Bash
docker compose up -d
```

Stop the environment:

``` Bash
docker compose down
```

View running containers:

``` Bash
docker ps
```

### 3.1 Environment Configuration

The repository already includes the required local development environment configuration.

No additional environment variable setup is required for standard local development.

If environment variables need to be changed in future, they are stored in:

```docker-compose.local.yml ```

## 4.0 Local Wordpress Site Setup
Ensure containers are running before opening local site.

Local Site: [localhost:8080](localhost:8080)

Local Site Admin: [localhost:8080/wp-admin/](localhost:8080/wp-admin/)

### 4.1 Activate the project theme

Inside WordPress Admin:

```Appearance -> Themes```

Activate:

```neve-fse-child```

This is CP3402-SupA's custom child theme

## 5.0 AWS Production Hosting

The production environment was hosted using Amazon Web Services (AWS) Elastic Compute Cloud (EC2).

---

### 5.1 Production Environment Overview

The production environment consisted of:

- AWS EC2 Ubuntu Server instance
- Docker and Docker Compose
- WordPress container
- MariaDB container
- GitHub repository integration
- Automated deployment workflow using GitHub Actions

This environment allowed the website theme code to be automatically deployed from GitHub to the live production server whenever updates were pushed to the main branch.

---

## 6.0 EC2 Instance Configuration

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

## 7.0 Docker Production Deployment

Docker was installed on the EC2 instance to host the production WordPress environment and uses the same `docker-compose.yml` configuration as the local development environment to ensure deployment consistency.

The following services were deployed:

- WordPress
- MariaDB

The Docker Compose configuration includes:

- Persistent MariaDB database volumes
- Mounted child theme directory
- PHP configuration mounting
- Container restart policies

Example Docker volume configuration:

```yaml
volumes:
  - db_data:/var/lib/mysql
```

This ensures WordPress database data persists between container restarts and deployments.

---

## 8.0 Production Theme Deployment

The WordPress child theme was stored within the GitHub repository and mounted into the WordPress Docker container using Docker volumes.

Example volume mount:

```yaml
volumes:
  - ./wp-content/themes:/var/www/html/wp-content/themes
```


## 7.0 Automated Deployment Workflow

A Continuous Deployment (CD) workflow was implemented using GitHub Actions.

The workflow automatically deploys changes to the production server whenever commits are pushed to the `main` branch.

Deployment workflow process:

```text
Local Development
        ↓
Git Commit
        ↓
Git Push to Development Branch on GitHub
        ↓
Pull Request from Development Branch to Main Branch
        ↓
GitHub Actions Workflow Trigger
        ↓
SSH Connection to AWS EC2
        ↓
git pull on Production Server from Main Branch
        ↓
Docker Container Restart
        ↓
Production Website Updated
```


### 7.1 SSH Authentication

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

### 7.2 GitHub Actions Workflow Configuration

A GitHub Actions workflow file was created within:

```text
.github/workflows/deploy.yml
```

The workflow automatically connects to the EC2 server and updates the production site.

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