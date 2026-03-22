## Task 1: The Problem

Run a Postgres or MySQL container

      - docker run -d --name my_mysql -e MYSQL_ROOT_PASSWORD=1234 mysql
      
Create some data inside it (a table, a few rows — anything)

     - docker exec -it my-mysql mysql -u root -p
     
         - CREATE DATABASE testdb;
            USE testdb;
            CREATE TABLE users (id INT AUTO_INCREMENT PRIMARY KEY, name VARCHAR(50));
            INSERT INTO users (name) VALUES ('Alice'), ('Bob');
            SELECT * FROM users;


Stop and remove the container

     - docker stop my-mysql
       docker rm my-mysql


Run a new one — is your data still there?

     -   docker run -d --name my-mysql -e MYSQL_ROOT_PASSWORD=secret mysql
         docker exec -it my-mysql mysql -u root -p
         SHOW DATABASES;

         = The testdb database and your users table are gone.

           That’s because containers use ephemeral storage — once removed, all data inside disappears.



## Task 2: Named Volumes

Create a named volume

    - docker volume create my-mysql-data
    
Run the same database container, but this time attach the volume to it

    - docker run -d --name my-mysql \
        -e MYSQL_ROOT_PASSWORD=secret \
        -v my-mysql-data:/var/lib/mysql \
        mysql

Add some data, stop and remove the container

     - docker exec -it my-mysql mysql -u root -p

         - CREATE DATABASE testdb;
            USE testdb;
            CREATE TABLE users (id INT AUTO_INCREMENT PRIMARY KEY, name VARCHAR(50));
            INSERT INTO users (name) VALUES ('Alice'), ('Bob');
            SELECT * FROM users;
            
       - docker stop my-mysql
         docker rm my-mysql

Run a brand new container with the same volume

    - docker run -d --name my-mysql2 \
        -e MYSQL_ROOT_PASSWORD=secret \
        -v my-mysql-data:/var/lib/mysql \
        mysql
        
     - docker exec -it my-mysql2 mysql -u root -p
     
     - check this :- SHOW DATABASES;
                        USE testdb;
                        SELECT * FROM users;

Is the data still there?

    - Yes , because we save the data inside the volume , so when the container got deleted it still show the data


## Task 3: Bind Mounts

Create a folder on your host machine with an index.html file

     - mkdir C:\Users\dibya\nginx-data
      - cd ngnix-data
      - notepad index.html
      - <!DOCTYPE html>
            <html>
              <head><title>My Test Page</title></head>
              <body><h1>Hello from Bind Mount!</h1></body>
            </html>

Run an Nginx container and bind mount your folder to the Nginx web directory

    - docker run -d --name my-nginx `
        -p 8080:80 `
        -v C:\Users\dibya\nginx-data:/usr/share/nginx/html `
        nginx

Access the page in your browser

      - http://localhost:8080

Edit the index.html on your host — refresh the browser

       - notepad index.html
       - <!DOCTYPE html>
            <html>
              <head><title>My Test Page</title></head>
              <body><h1>Hello Everyone from Bind Mount!</h1></body>
            </html>
            
 Volumes → Docker manages the storage, good for persistence (databases).

 Bind mounts → You manage the storage, good for live editing (web development).

## Task 4: Docker Networking 

List all Docker networks on your machine

      - docker network ls

     = bridge → the default network for containers.

       host → shares the host’s network stack.
      
       none → isolates containers completely.
       
Inspect the default bridge network

    - docker network inspect bridge
    
Run two containers on the default bridge — can they ping each other by name?

    - docker run -dit --name container1 busybox sh
    - docker run -dit --name container2 busybox sh

    => Ping means when you are in one container and from here you send some message like "hello" to another container and another one recive and send the   reply than it's reachable

    - docker exec -it container1 ping container2
    
Run two containers on the default bridge — can they ping each other by IP?


      - First we have to find out the ip address of container 2 to make reach our message

           - docker inspect -f '{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}' container2

            - docker exec -it container1 ping <ip_address>

## Task 5: Custom Networks

Create a custom bridge network called my-app-net

    - docker network create --driver bridge my-app-net

Run two containers on my-app-net

    - docker run -dit --name containerA --network my-app-net busybox sh
    - docker run -dit --name containerB --network my-app-net busybox sh

Can they ping each other by name now?

    - docker exec -it containerA ping containerB
      = it's work
      
Write in your notes: Why does custom networking allow name-based communication but the default bridge doesn't?

    -  custom networks add Docker’s DNS service, so containers can talk by name. The default bridge is more limited and only supports IP‑based communication.


## Task 6: Put It Together

Create a custom network

     - docker network create --driver bridge my-app-net

Run a database container (MySQL) on that network with a volume for data

      - docker volume create my-db-data

            = docker run -d --name my-db \
              --network my-app-net \
              -e MYSQL_ROOT_PASSWORD=secret \
              -v my-db-data:/var/lib/mysql \
              mysql

Run an app container (use any image) on the same network

      = docker run -dit --name my-app \
        --network my-app-net \
        busybox sh

Verify the app container can reach the database by container name

     - docker exec -it my-app ping my-db
         = Yes it works
