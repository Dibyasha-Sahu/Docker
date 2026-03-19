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

