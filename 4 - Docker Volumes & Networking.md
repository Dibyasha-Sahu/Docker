## Task 1: The Problem

Run a Postgres or MySQL container

      - docker run -d --name my_mysql -e MYSQL_ROOT_PASSWORD=1234 mysql
Create some data inside it (a table, a few rows — anything)
Stop and remove the container
Run a new one — is your data still there?
