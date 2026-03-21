## Task 1: Your First Dockerfile

Create a folder called my-first-image

      - If you use to create the container by use docker create then this is the process

                = docker create --name my_first_image ubuntu bash
                = docker start -ai my_first_image

                               OR
      - if you want create nad start the linux terminal than do this:

               = docker run -it --name my_first_image ubuntu bash 
                              
Inside it, create a Dockerfile that:

Uses ubuntu as the base image 

Installs curl

Sets a default command to print "Hello from my custom image!"

      - mkdir C:\Users\dibya\my-first-image

      - cd C:\Users\dibya\my-first-image

      - notepad Dockerfile

       * FROM ubuntu:latest

        RUN apt-get update && apt-get install -y curl

        CMD ["echo", "Hello from my custom image!"]

Build the image and tag it my-ubuntu:v1

      - docker build -t my-ubuntu:v1 .
      
Run a container from your image

      - docker images (to verify)

      - docker run my-ubuntu:v1  (Run the container)


## Task 2: Dockerfile Instructions

Create a new Dockerfile that uses all of these instructions:

      - mkdir custom_page
      - cd cuustom_page
      -notepad custom_page (notepad will open )

FROM — base image

     - FROM ubuntu:latest

WORKDIR — set working directory

    - WORKDIR /app

RUN — execute commands during build

     - RUN apt-get update && apt-get install -y curl

EXPOSE — document the port

    - EXPOSE 8080
     
COPY — copy files from host to image

    - COPY index.html /usr/src/app/index.html

CMD — default command

    - CMD ["echo", "Hello from my custom image!"]


Create the file to copy


    - notepad index.html

    - it will open the notepad and paste this:

          <html>
            <body>
               <h1>Hello from my copied file!</h1>
            </body>
          </html>

Verify both files exist

     - dir
     
Build the image

    - docker build -t my-ubuntu:v1 .

Check the image list

    - docker images

Run the container

    - docker run my-ubuntu:v1


## Task 3: CMD vs ENTRYPOINT 

Create an image with CMD ["echo", "hello"] — run it, then run it with a custom command. What happens?

     - mkdir task3-image
     - cd task3-image
     - notepad Dockerfile
     
     - FROM ubuntu:latest
     - CMD ["echo", "hello"]

     - docker build -t task3-image .   (it will use the :latest tag by default)
     - docker run task3-image
     - docker run task3-image echo "how are you"
     
Create an image with ENTRYPOINT ["echo"] — run it, then run it with additional arguments. What happens?

    - mkdir task3-entrypoint
    - cd task3-entrypoint
    - notepad Dockerfile
       
        FROM ubuntu:latest
        ENTRYPOINT ["echo"]

     - docker build -t task3-entrypoint
     - docker run task3-entrypoint
     - docker run task3-entrypoint how are you
     
Write in your notes: When would you use CMD vs ENTRYPOINT?

    - CMD
       
       Dockerfile: CMD ["echo", "hello"]

       Run: docker run myimage → prints hello

       Run: docker run myimage echo "bye" → prints bye (CMD was replaced)

       CMD = default, but changeable

       CMD is like saying “If you don’t tell me, I’ll do this.”
       
    - ENTRYPOINT
       
       Dockerfile: ENTRYPOINT ["echo"]

       Run: docker run myimage hello → prints hello

       Run: docker run myimage bye → prints bye

       You can’t replace echo unless you use special flags.

       ENTRYPOINT is like saying “No matter what, I’ll do this — you can only add details.”

## Task 4: Build a Simple Web App Image

     - mkdir task4-website
     - cd task4-website

Create a small static HTML file (index.html) with any content

     - notepad index.html
     - <html>
         <body>
             <h1>Welcome to My Website!</h1>
             <p>This is served by Nginx inside Docker.</p>
         </body>
       </html>


Write a Dockerfile that:

      - notepad Dockerfile

Uses nginx:alpine as base
Copies your index.html to the Nginx web directory

      - FROM nginx:alpine

        # Copy your HTML file into Nginx's default web directory
        COPY index.html /usr/share/nginx/html/index.html

Build and tag it my-website:v1

      - docker build -t my-website:v1 .
      
Run it with port mapping and access it in your browser

      - docker run -d -p 8080:80 my-website:v1

## Task 5: .dockerignore

Create a .dockerignore file in one of your project folders

       - use the task4-website folder
          - notepad .dockerignore

              node_modules
              .git
              *.md
              .env


Add entries for: node_modules, .git, *.md, .env

      - node_modules → ignores the Node.js dependency folder (often huge).

        .git → ignores your Git repo metadata.
        
        *.md → ignores all Markdown files (like README.md).
        
        .env → ignores environment variable files (often contain secrets).
        
Build the image — verify that ignored files are not included

       - docker build -t my-website:v2 .

       - Before running this cmd you make sure that you previous contanier should stop and remove

           docker run -d -p 8080:80 my-website:v2


## Task 6: Build Optimization

Build an image, then change one line and rebuild — notice how Docker uses cache

     - mkdir cache-image
     - cd cache-image
     - notepad Dockerfile
            FROM ubuntu:latest
            RUN apt-get update && apt-get install -y curl
            COPY index.html /usr/share/nginx/html/index.html

      - notepad index.html
            <html>
              <body>
                <h1>Welcome to My Website!</h1>
                <p>This is served by Nginx inside Docker.</p>
              </body>
            </html>

      - docker build -t cache-image:v1 .

      ## It will show somthing like this on fresh creation:

           Step 1/3 : FROM ubuntu:latest
                         ---> 2dc39ba059dc
                        Step 2/3 : RUN apt-get update && apt-get install -y curl
                         ---> Running in 123abc
                         ---> 456def
                        Step 3/3 : COPY index.html /usr/share/nginx/html/index.html
                         ---> 789ghi
                        Successfully built 789ghi
                        Successfully tagged cache-demo:v1

      


Reorder your Dockerfile so that frequently changing lines come last

      - FROM ubuntu:latest
        COPY index.html /usr/share/nginx/html/index.html
        RUN apt-get update && apt-get install -y curl

        -docker build -t chache-image:v3 .
        

Write in your notes: Why does layer order matter for build speed?

      - Docker builds in layers.

       If one layer changes, all later layers rebuild.
      
       Put stable steps first (like installing packages).
      
       Put changing steps last (like copying source code).
      
       This keeps builds fast by reusing cached layers.

       - Rule of thumb: Stable first, changing last → faster builds.
