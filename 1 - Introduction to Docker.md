## 1. What is a container and why do we need them?

A container is a lightweight, standalone package that includes everything needed to run a piece of software: code, runtime, system tools, libraries, and settings.

Why we need them:

     - Consistency
     - Efficiency
     - Isolation

## 2. Containers vs. Virtual Machines

Containers
           
           - Shares the Host OS kernel.
           - Lightweight
           - Efficiency	High

Virtual Machine

          - Each has its own full Guest OS.
          -	Heavy (GBs).
          -	Lower (high overhead).

## Docker Architecture

Docker Daemon = 
            
            = run on host OS
            = it is responsible for running containers and managing docker services
            = Docker demons can communicate with other demons

Docker Client =

           = Docker users can interact with the docker daemon through a client
           = Docker client uses cmds and RESTAPI to communicate with the docker daemon.
           = Docker clients can communicate with more than one daemon.




## Task 2: Install Docker

Install Docker on windows

Verify the installation 

          - docker --version
          - docker info (to check docker engine information)

Run the hello-world container

           - Open the docker desktop terminal and run <<docker run hello-world>>
           - OUTPUT :- My hello-world image is not present locally so docker contacted to docker hub/registry and download the image
                     - And than Docker create and container and then container run and stopped


## Task 3: Run Real Containers

Run an Nginx container and access it in your browser

           - docker run -d -p 80:80 nginx
           - docker ps (to check wheater the nginix is running or not)
           - Go to browser and run << http://localhost >>

Run an Ubuntu container in interactive mode — explore it like a mini Linux machine

          - docker run -it ubuntu bash (create a container of ubuntu)

List all running containers

          - docker ps
          
List all containers (including stopped ones)

          - docker ps -a

Stop and remove a container

          - docker stop <Container_Id>  (stop)
          - docker rm <Container_Id> (remove)


## Task 4: Explore

Run a container in detached mode — what's different?

         - docker run -d ngnix ( " You don’t see the container’s output directly in your terminal. Instead, you get the container ID. ")

Give a container a custom name

         - docker run -d --name mynginx nginx 

Map a port from the container to your host

         - docker run -d -p 80:80 --name webserver nginx

Check logs of a running container

         - docker logs webserver (See what the container is printing)

Run a command inside a running container

         - docker exec -it webserver(container_id) bash





