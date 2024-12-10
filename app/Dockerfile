# node.js img based on alpine linux 3.11
FROM node:12.22.1-alpine3.11

# installs py2 and build-base package in the img (--no-cache means it won't store temporal files from the installation, keeping the img light)
RUN apk add --no-cache python2 build-base

# sets the workspace name for the img in /app. All the following comands will executo within this directory
WORKDIR /app

# copy all files and directories within the context to the workspace /app within the container
COPY . .

# compiles the production dependencies of the app with yarn
RUN yarn install --production

# sets the initial comand that will run when te container starts. This will execute the file "/app/src/index.js"
CMD ["node", "/app/src/index.js"]

# in cmd (standing in this directory):
# build the img (not run) with "simple_app" tag on the img
    # 1) docker build -t simple_app .
# run the img with "my_container" tag on the container
    # 2)a) docker run --name my_container simple_app
    # 2)b) docker run -d --name my_container simple_app (if we want it to run on background)
    # 2)c) docker run -d --name my_container -p 3000:3000 simple_app (to run on background in the localhost:3000 or 127.0.0.1)
# data persistence, first we create a container with an associated local db (it's in the local path specified that will be mounted in: /etc/todos within the container). After that, we can run another container with the same volume to conserve the data bidirectionally:
    # 3) docker run -d -p 3000:3000 --name my_container -v C:\Users\elias\Desktop\Code\Docker-Basics\app\etc:/etc/todos simple_app
    # 4) docker run -dp 3000:3000 --name my_container -v C:\Users\elias\Desktop\Code\Docker-Basics\app\etc:/etc/todos simple_app
# modifying app and data persistance: (same as 3 and 4, but now we must specify where is the modified file and where that it will be mounted in: /app/src within the container 
    # 5) docker run -dp 3000:3000 --name my_container -v C:\Users\elias\Desktop\Code\Docker-Basics\app\etc:/etc/todos -v C:\Users\elias\Desktop\Code\Docker-Basics\app\src:/app/src simple_app
# rebuild the img with the modified app. Then we can use that new img for data persistance without specifying the modified files:
    # 6) docker build -t simple_app:v2
    # 7) docker run -dp 3000:3000 --name my_container -v C:\Users\elias\Desktop\Code\Docker-Basics\app\etc:/etc/todos simple_app:v2
# now we can push that img to docker hub (its importat to tag it)
    # 8) docker login
    # 9) docker tag 0409ee97d66b elsmims/simple_app:v2
    # 10) docker push elsmims/simple_app:v2
# now the img is public and can be pulled from anywhere
    # https://hub.docker.com/r/elsmims/simple_app/tags

# real use cases (multi containers at the same time):
    # First we create a network so they can communicate
    #---1) docker network create net_todo_app

    # (-d) we create a container attached to that network, tagging it with alias "mysql" for the host
    # (-v) we mount a volume "todo_mysql_data" (for data persitance) in the local host within /var/lib/mysql in the container (all the details can be found in the mysql docker hub doc)
    # (-e) we set some environment variables for the root password for MySQL(-e) and another env var to create a db called "todos" on the first container run
    #---2) docker run --name my_container_3 -d --network net_todo_app --network-alias mysql -v todo_mysql_data:/var/lib/mysql -e MYSQL_ROOT_PASSWORD=secret -e MYSQL_DATABASE=todos mysql:5.7
    # If we want, we can log into the mysql instance within the container with:
    #---2)a) docker exec -it 36469f0d15aa mysql -p    (password: secret)
    #---2)b show databases;  (this will show that the mysql db "todos" is created)
    # We run "nicolaka/netshoot" img from the docker hub within "pulled_container" with the associated network to link it with our mysql
    #---3) docker run --name pulled_container -it --network net_todo_app nicolaka/netshoot
    #---4) dig mysql (will resolve the ip of the mysql container)
    # we create the container "multi_container_app" linked to our network "net_todo_app" and connected to mysql. Using the "simple_app:v2" img
    # (-e) host will be the alias of our mysql container that we set on my_container_3
    # (-e) user = root
    # (-e) pass = MYSQL_ROOT_PASSWORD that we set on my_container_3
    #---5) docker run -dp 3000:3000 --name multi_container_app --network net_todo_app -e MYSQL_HOST=mysql -e MYSQL_USER=root -e MYSQL_PASSWORD=secret -e MYSQL_DB=todos simple_app:v2
    # now we can create another container with the same img and parameters, so it will be linked to the mysql db from my_container_3
    #---5)a) docker run -dp 3000:3000 --name multi_container_app_2 --network net_todo_app -e MYSQL_HOST=mysql -e MYSQL_USER=root -e MYSQL_PASSWORD=secret -e MYSQL_DB=todos simple_app:v2

# docker compose simplifies lot of this previous stuff