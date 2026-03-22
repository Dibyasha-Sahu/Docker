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



