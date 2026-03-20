## Task 1: Docker Images

Pull the nginx, ubuntu, and alpine images from Docker Hub

        - docker pull nginx
        - docker pull ubuntu
        - docker pull alpine

List all images on your machine 

       - docker images

Compare ubuntu vs alpine — why is one much smaller?

      - Ubuntu = Think of Ubuntu like a full toolbox.

                . It comes with many tools, libraries, and utilities already installed.

                . That’s why it’s bigger (around 70 MB).

                . Good if you want everything ready to go, but heavier to download and run.

      - Alpine = Alpine is like a tiny pocket toolkit.

                . It only has the bare minimum needed to run Linux.

                . That’s why it’s super small (around 5 MB).

                . Faster to pull and run, but if you need extra tools, you’ll have to install them yourself.

      = Ubuntu → bigger, easier to use right away.

      = Alpine → smaller, faster, but you may need to add things manually.

Inspect an image 

      - docker inspect nginx

      This shows detailed JSON info, including:

       Image ID and size

       Environment variables

       Default command (CMD)

       Exposed ports

       Layers that make up the image

Remove an image you no longer need

       - docker rmi alpine

          . clean up unused images.(If a container is still using it, you’ll need to stop/remove the container first.)

## Task 2: Image Layers

Run docker image history nginx — what do you see?

       - docker image history nginx

         = This shows the history of the image — each line is a layer that makes up the final image.

Each line is a layer. Note how some layers show sizes and some show 0B

       - IMAGE      CREATED         CREATED BY                                     SIZE      COMMENT

          97        3 days ago     CMD ["nginx" "-g" "daemon off;"]                0B        buildkit

        <missing>  3 days ago   COPY 30-tune-worker-processes.sh /docker-ent…      16.4kB    buil

Write in your notes: What are layers and why does Docker use them?

      - What are layers?

         = Each Docker image is built in layers.

           Every command in a Dockerfile (RUN, COPY, ADD) creates a new layer.

           Layers stack on top of each other to form the final image.

           Example: Ubuntu base layer → add Nginx → add config → final image.

      - Why does Docker use layers?
      
           = Efficiency: If multiple images share the same base (like Ubuntu), Docker reuses those layers instead of downloading them again.

             Caching: When rebuilding, unchanged layers don’t need to be rebuilt.

             Smaller downloads: Only new or changed layers are pulled.

             Portability: Layers can be distributed and reused across different images.  


## Task 3: Container Lifecycle

Create a container (without starting it)

      - docker create --name lifecyle-test ubuntu

Start the container

     - docker start lifecycle-test
     
Pause it and check status

     - docker pause lifecycle-test

        = doker ps ( shows Paused status.)

Unpause it

    -  docker unpause lifecycle-test

      = docker ps ( shows Up again.)

Stop it

    - docker stop lifecycle-test

      = docker ps -a ( shows Exited status.)

Restart it

    - docker restart lifecycle-test

       = docker ps (shows Up.)

Kill it

     - docker kill lifecycle-test

        = docker ps -a → shows Exited (0) or similar.

Remove the container

    - docker rm lifecycle-test

      = docker ps -a (it’s gone.)


## Task 4: Working with Running Containers

Run an Nginx container in detached mode
     
     - docker run -d --name mine_nginx -p 8080:80 nginx
     
View its logs

     - docker logs mine_nginx(or container_id)

View real-time logs (follow mode)

     - docker logs -f mine_nginx
     
Exec into the container and look around the filesystem

     - docker exec -it mine_nginx bash
     
Run a single command inside the container without entering it

     - docker exec mine_nginx ls /usr/share/nginx/html

Inspect the container — find its IP address, port mappings, and mounts

      - docker inspect mine_nginx


## Task 5: Cleanup


Stop all running containers in one command

     - docker stop $(docker ps -q)
     
Remove all stopped containers in one command

     - docker ps $(docker ps -aq)
     
Remove unused images

     - docker image prune -a

Check how much disk space Docker is using

     - docker system df

    


