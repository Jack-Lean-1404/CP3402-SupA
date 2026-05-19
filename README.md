# CP3402 SupA

# Project Overview

This project is a custom WordPress website developed for U3A using a block-based Full Site Editing (FSE) workflow. The site uses a custom child theme built on top of the Neve FSE parent theme and is developed locally using Docker before being deployed to production.

## Deployment

## Theme Development


1. install required tools
2. clone repo to local machine
3. config enviroment variables
4. configure docker compose local yml
5. start local enviroment
6. verify the stack
7. open site
8. log into wordpress
9. activate the project theme

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

Local Site: ```localhost:8080```

Local Site Admin: ```localhost:8080/wp-admin/```

## 5.1 Activate the project theme

Inside WordPress Admin:

```Appearance -> Themes```

Activate:

```neve-fse-chile```

This is CP3402-SupA's custom child theme

