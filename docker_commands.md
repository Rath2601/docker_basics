## COMMANDS

1. **docker login -u <username>** : logged into the registry and authorized using username and password
2. **docker build -t <image-name> <path>** (ex: docker build -t dock-project .) : build an image out of the config in Dockerfile.
3. **docker run -d -p 8000:8080 --name dock-project dock-project** : get it from hub and then run the image in a container. (-d refers to detach the console from the container)
4. **docker attach <container ID>**: to attach the console again with container.
5. **docker exec <image name>** : to execute a command in a **running container**.
6. **docker stop <container ID>** : we can stop the container
7. **docker rm -f <container ID>** : we can also forcibly remove container. (we can remove multiple containers)
8. **docker rmi <image name>** : to remove an image it should not be running and dependent containers also removed.
9. **docker images** : to list the images in the local.
10. **docker container ls** : list all the running containers in the local
11. **docker commit <container ID> <username>/<image_name>** : to create an image out of **an edited container**.
12. **docker ps -a** : list all the containers even the stopped one.
13. **docker compose** : used for managing **multiple services** that often work together (like a web app, a database, and a caching layer), **each in its own container**.
     (Example: A web app container, a database container, and a Redis container working together.)
     * **docker compose build**: only builds the images, does not start the containers
     * **docker compose up**: builds the images if the images do not exist and starts the containers
     * **docker compose up --build**: If you add the --build option, it is forced to build the images even when not needed
     * **docker compose up --no-build**: skips the image build process.
14. **docker build -t rath2601/dock-project:latest .** (while pushing we need to associate it with username)
15. **docker push rath2601/dock-project:latest**
16. **docker pull <image name>** : get it from hub only.
17. **docker export --output="latest.tar" <container ID / container name>** : to save a tar file of the given active/inactive container locally.
18. **docker import <absolute-path-of-tar-file>** : to create a local image out of a tar file.

19. **docker tag <YOUR_DOCKER_USERNAME>/docker-quickstart <YOUR_DOCKER_USERNAME>/docker-quickstart:1.0** : Docker tags allow you to label and version your images while pushing to repository

20. **docker swarm** : **Cluster of docker engine in different virtual/physical machine**.
    Docker Swarm is a native **clustering and orchestration tool** for Docker containers. It allows you to manage a group of **Docker hosts** (machines running Docker) as a single, virtual host. By using Docker Swarm, you can automate the deployment, scaling, and management of containerized applications across multiple nodes.
    
  * **docker swarm init --advertise-addr <Master Private IP || ip-address>** : to initiate swarm as a manager
  * **docker swarm join --token <worker-token> <ip-address>** : to join as a worker node.
  * **docker swarm join-token manager** : to get manager token.
  * **docker swarm join --token <manager-token> <ip-address>** : to join as a manager node.
  * **docker swarm leave --force** : to leave the node. (--force applicable for manager)
  * **docker node ls** : list all nodes in cluster
  * **docker service create --replicas 2 -p 80:80 --name <container_name> <image_name>** : to create a service 
  * **docker service create --mode global -p 80:80 --name <container_name> <image_name>** : to create a service globally
  * **docker service ps <container_name>** : list all services as per node.
  * **docker service rm <container_name>** : remove a particular service from the cluster
  * **docker service update --image <image_name> <container_name>** : updates the image of a Docker service to a new version.
  * **docker service rollback <container_name>** : rolls back a Docker service to its previous version.
  * **docker promote <node_name>** : promotes a Docker worker node to a manager node in the swarm.
  * **docker node update --availability pause <node_name>** : pauses a Docker node, preventing it from running new tasks.
  * **docker node update --availability drain <node_name>** : stop a docker node from running services.
  * **docker network ls** : Lists all Docker networks.
  * **docker service rm <service_name>** : removes a Docker service from the swarm.
  * **docker service scale <service_name>=<count>** : scales a Docker service to the specified number of replicas.
  * **docker swarm join-token worker** : displays the command to join a Docker swarm as a worker node.

### **DOCKER NETWORKING** :
  *  ability for containers to **connect to and communicate with each other**, or to non-Docker workloads.

### **NOTE**:
 * **docker run ubuntu** : When we run a container from an ubuntu image it exits immediately(stopped). because dockers(container) are meant to host process such as web server, database, and other computational task. **Not meant to host OS**. Container lives as long as process inside it is alive.
 * A stopped container always stays in the memory of the disk.
 * Mostly we'll use version 3 in docker compose yaml file, which supports swarm also.

### **DOUBTS** :  
 * **why we use docker swarm instead of docker compose** :
    * can scale individual containers on a single machine, but is limited to the capacity of that single machine. (**scalable**)
    * If your single machine goes down, all your services are unavailable in docker compose. docker swarm is **distributed and fault-tolerant**
    * the whole setup is **decentralized**. A node can be accessed from any node.
    * Application hosted on one node can be accessed through other node , if there are connected in swarm network.
    * Swarm provides built-in **load balancing**.
    * Swarm offers built-in **service discovery** and networking that work seamlessly across nodes in a multi-host environment.
    * Docker Swarm allows for **rolling updates** to your services, ensuring no downtime during deployment by gradually replacing old containers with new ones.

## Dockerizing a spring boot application with docker desktop

1. create a Dockerfile in springboot project root directory and add fields such as

```
FROM openjdk:17-jdk-alpine
ARG JAR_FILE=target/*.jar
COPY ./target/dock-project.jar dock-project.jar
ENTRYPOINT ["java","-jar","/dock-project.jar"]

(or)

FROM openjdk:17-jdk-alpine
MAINTAINER baeldung.com
EXPOSE 8080
COPY target/docker-message-server-1.0.0.jar message-server-1.0.0.jar || ADD target/docker-message-server-1.0.0.jar message-server-1.0.0.jar
ENTRYPOINT ["java","-jar","/message-server-1.0.0.jar"]

```

2. login to dockerhub & ensure docker desktop is up.
3. build docker image out of the Dockerfile.
4. run container with that image and test whether our container works as expected.
