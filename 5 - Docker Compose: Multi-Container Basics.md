## Task 1: Install & Verify

Check if Docker Compose is available on your machine

     - docker compose version

Verify the version

     - Docker Compose version v2.40.0-desktop.1  (My version)


## Task 2: Your First Compose File

Create a folder compose-basics

       - mkdir compose-basics
         cd compose-basics

Write a docker-compose.yml that runs a single Nginx container with port mapping

       - notepad docker-compose.yml

          = version: "3.9"
              services:
                web:
                  image: nginx
                  ports:
                    - "8080:80"

        - To verify = dir


Start it with docker compose up

      - docker compose up -d

Access it in your browser

     - http://localhost:8080

Stop it with docker compose down

     - docker compose down

. Compose lets you define multi‑container apps in one YAML file.

## Task 3: Two-Container Setup

Write a docker-compose.yml that runs:

A WordPress container
A MySQL container
They should:

Be on the same network (Compose does this automatically)
MySQL should have a named volume for data persistence
WordPress should connect to MySQL using the service name

    = mkdir folder_name
      cd folder_name
      notepad docker-compose.yml

     = version: '3.8'
          services:
            db:
              image: mysql:5.7
              restart: always
              environment:
                MYSQL_DATABASE: wordpress
                MYSQL_USER: wordpress
                MYSQL_PASSWORD: wordpress
                MYSQL_ROOT_PASSWORD: rootpassword
              volumes:
                - db_data:/var/lib/mysql
          
            wordpress:
              image: wordpress:latest
              restart: always
              ports:
                - "8080:80"
              environment:
                WORDPRESS_DB_HOST: db:3306
                WORDPRESS_DB_USER: wordpress
                WORDPRESS_DB_PASSWORD: wordpress
                WORDPRESS_DB_NAME: wordpress
              depends_on:
                - db
          
          volumes:
            db_data:

Start it, access WordPress in your browser, and set it up.

       = docker compose up -d
       
 access WordPress in your browser

       = http://localhost:8080


Verify: Stop and restart with docker compose down and docker compose up — is your WordPress data still there?

     = docker compose down
       docker compose up -d
       http://localhost:8080

## Task 4: Compose Commands

Practice and document these:

Start services in detached mode

    = docker compose up -d
    
View running services

    = docker compose ps
    
View logs of all services

    = docker compose logs
    
View logs of a specific service

    = docker compose logs worpress
    
Stop services without removing

    = docker compose stop
    
Remove everything (containers, networks)

    = docker compose down
    
Rebuild images if you make a change

    = docker compose build

 * docker compose down -v   if you want to remove volumes too


## Task 5: Environment Variables

Add environment variables directly in your docker-compose.yml

     - Add environment variables directly in docker-compose.yml

          = version: '3.8'
               services:
                 db:
                   image: mysql:5.7
                   restart: always
                   environment:
                     MYSQL_DATABASE: wordpress
                     MYSQL_USER: wordpress
                     MYSQL_PASSWORD: wordpress
                     MYSQL_ROOT_PASSWORD: rootpassword
                   volumes:
                     - db_data:/var/lib/mysql
               
                 wordpress:
                   image: wordpress:latest
                   restart: always
                   ports:
                     - "8080:80"
                   environment:
                     WORDPRESS_DB_HOST: db:3306
                     WORDPRESS_DB_USER: wordpress
                     WORDPRESS_DB_PASSWORD: wordpress
                     WORDPRESS_DB_NAME: wordpress
                   depends_on:
                     - db
               
               volumes:
                 db_data:

Create a .env file and reference variables from it in your compose file

       = Create a file named .env in the same directory:

               MYSQL_DATABASE=wordpress
               MYSQL_USER=wordpress
               MYSQL_PASSWORD=wordpress
               MYSQL_ROOT_PASSWORD=rootpassword
               WORDPRESS_PORT=8080

     = Update your docker-compose.yml to use these variables:
        
        version: '3.8'
          services:
            db:
              image: mysql:5.7
              restart: always
              environment:
                MYSQL_DATABASE: ${MYSQL_DATABASE}
                MYSQL_USER: ${MYSQL_USER}
                MYSQL_PASSWORD: ${MYSQL_PASSWORD}
                MYSQL_ROOT_PASSWORD: ${MYSQL_ROOT_PASSWORD}
              volumes:
                - db_data:/var/lib/mysql
          
            wordpress:
              image: wordpress:latest
              restart: always
              ports:
                - "${WORDPRESS_PORT}:80"
              environment:
                WORDPRESS_DB_HOST: db:3306
                WORDPRESS_DB_USER: ${MYSQL_USER}
                WORDPRESS_DB_PASSWORD: ${MYSQL_PASSWORD}
                WORDPRESS_DB_NAME: ${MYSQL_DATABASE}
              depends_on:
                - db
          
          volumes:
            db_data:


Verify the variables are being picked up

      = docker compose config
      = docker compose exec db env

