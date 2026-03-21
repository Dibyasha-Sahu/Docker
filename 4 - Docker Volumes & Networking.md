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

    - Yes , because ew save the data inside the volume , so when the container got deleted it still show the data


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


Task 4: Docker Networking Basics
List all Docker networks on your machine
Inspect the default bridge network
Run two containers on the default bridge — can they ping each other by name?
Run two containers on the default bridge — can they ping each other by IP?
