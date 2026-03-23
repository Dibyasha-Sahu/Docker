## Task 1: Build Your Own App Stack

Create a docker-compose.yml for a 3-service stack:

     - mkdir myapp-stack
       cd myapp-stack

      - notepad docker-compose.yml

         =  version: "3.9"
            services:
              web:
                build: ./web
                ports:
                  - "5000:5000"
                environment:
                  - DATABASE_URL=mysql://root:root@db:3306/mydb
                  - REDIS_URL=redis://cache:6379/0
                depends_on:
                  - db
                  - cache
            
              db:
                image: mysql:8
                restart: always
                environment:
                  MYSQL_ROOT_PASSWORD: root
                  MYSQL_DATABASE: mydb
                volumes:
                  - db_data:/var/lib/mysql
            
              cache:
                image: redis:7
                restart: always
            
            volumes:
              db_data:

      
A web app (use Python Flask, Node.js, or any language you know)
 - A database (Postgres or MySQL)
 - A cache (Redis)

       - mkdir web  (inside the myapp-stack )
       - notepad Dockerfile

         = FROM python:3.11-slim

            WORKDIR /app
            
            COPY requirements.txt .
            RUN pip install --no-cache-dir -r requirements.txt
            
            COPY app.py .
            
            CMD ["python", "app.py"]

        - notepad requirements.txt

           =  flask
              mysql-connector-python
              redis

        - notepad app.py

            = from flask import Flask
              import mysql.connector
              import redis
              
              app = Flask(__name__)
              
              # Connect to MySQL
              conn = mysql.connector.connect(
                  host="db",
                  user="root",
                  password="root",
                  database="mydb"
              )
              cur = conn.cursor()
              
              # Connect to Redis
              r = redis.Redis(host="cache", port=6379, db=0)
              
              @app.route("/")
              def hello():
                  # Simple DB query
                  cur.execute("SELECT NOW();")
                  db_time = cur.fetchone()[0]
              
                  # Simple Redis set/get
                  r.set("greeting", "Hello from Redis!")
                  redis_msg = r.get("greeting").decode("utf-8")
              
                  return f"Hello World!<br>DB Time: {db_time}<br>{redis_msg}"
              
              if __name__ == "__main__":
                  app.run(host="0.0.0.0", port=5000)


        - docker-compose up --build
        - http://localhost:5000


## Task 2: depends_on & Healthchecks

Add depends_on to your compose file so the app starts after the database
Add a healthcheck on the database service

    - In your docker-compose.yml, update the db service with a healthcheck. For MySQL:

       = db:
          image: mysql:8
          restart: always
          environment:
            MYSQL_ROOT_PASSWORD: root
            MYSQL_DATABASE: mydb
          volumes:
            - db_data:/var/lib/mysql
          healthcheck:
            test: ["CMD", "mysqladmin", "ping", "-h", "localhost", "-uroot", "-proot"]
            interval: 10s
            timeout: 5s
            retries: 5

Use depends_on with condition: service_healthy so the app waits for the database to be truly ready, not just started

      - Update the web service so it waits until the DB is healthy:  (inside docker-compose.yml)

         = web:
            build: ./web
            ports:
              - "5000:5000"
            environment:
              - DATABASE_URL=mysql://root:root@db:3306/mydb
              - REDIS_URL=redis://cache:6379/0
            depends_on:
              db:
                condition: service_healthy
              cache:
                condition: service_started

Test: Bring everything down and up — does the app wait for the DB?


        - docker-compose down
        - docker-compose up --build

## Task 3: Restart Policies

Add restart: always to your database service

      = db:
          image: mysql:8
          restart: always
          environment:
            MYSQL_ROOT_PASSWORD: root
            MYSQL_DATABASE: mydb
          volumes:
            - db_data:/var/lib/mysql
            
Manually kill the database container — does it come back?

        - docker-compose up --build
        - docker kill <db_container_name>

Try restart: on-failure — how is it different?

         - update in docker-compose.yml
         
         - db:
            image: mysql:8
            restart: on-failure
            environment:
              MYSQL_ROOT_PASSWORD: root
              MYSQL_DATABASE: mydb
            volumes:
              - db_data:/var/lib/mysql


              = docker-compose down
                docker-compose up --build

              = docker kill <db_container_name>


Write in your notes: When would you use each restart policy?

= restart: always

. Container restarts no matter what (manual kill, crash, system reboot).

. Good for critical services like databases, caches, or message brokers.

restart: on-failure

. Restarts only if the container exits with an error code.

. Useful for batch jobs or scripts that should retry on errors but not run forever.

## Task 4: Custom Dockerfiles in Compose

Instead of using a pre-built image for your app, use build: in your compose file to build from a Dockerfile

     - web:
        build: ./web
        ports:
          - "5000:5000"
        environment:
          - DATABASE_URL=mysql://root:root@db:3306/mydb
          - REDIS_URL=redis://cache:6379/0
        depends_on:
          db:
            condition: service_healthy
          cache:
            condition: service_started

            = build: ./web → tells Compose to build an image from the Dockerfile inside the web/ folder.

Make a code change in your app

       - edit web/app.py:
       - @app.route("/")
            def hello():
                cur.execute("SELECT NOW();")
                db_time = cur.fetchone()[0]
                r.set("greeting", "Hello from Redis!")
                redis_msg = r.get("greeting").decode("utf-8")
                return f"Hello Docker Compose!<br>DB Time: {db_time}<br>{redis_msg}"

Rebuild and restart with one command


       - docker-compose up --build
       - http://localhost:5000






   

