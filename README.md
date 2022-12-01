# Ansible-docker-compose-deployment :
- By using the ansible play book, up the docker compose file.And run the container by using the ansible play book by using the ansible modules and docker compose file 

- in this we are write the docker compose file and ansible play book for run the docker container by using docker compose.

## Docker-compose

- Docker Compose is a tool that was developed to help define and share multi-container applications.

- With Compose, we can create a YAML file to define the services and with a single command, can spin everything up or tear it all down.
- [click here](https://docs.docker.com/get-started/08_using_compose/)

### Docker-compose-file 

- In this example nginx is taken as a docker immage for running the nginx container 

- Create a one directory by using the linux command `mkdir` named as `ansible-docker-compose-deployment`.

- After creating the directory `cd` into the `ansible-docker-compose-deployment`. And create one file named as `docker-compose.yml`.

- In that file write a docker compose file for the required services.[click here](https://docs.docker.com/get-started/08_using_compose/). more info

- In this assigenment we taken a `nginx` docker image, so we are write docker file like this. shown below
```
version: "3.3"
services:
  nginx_container:
    image: nginx:latest
    ports:
      - 80:80
```

- In this above `docker-compose.yml` file `version` is the docker compose version it is "3.3"

- In this file services means web services , dbservices ,and applications. in this file we are used `nginx` docker image.

- And which port the application is running it is mentioned in `ports` instruction.

- The `docker-compose.yml`file was wrote successfully .

> **Note** The docker compose service`docker-compose.yml`is run/up by using the command `docker compose up`. 
  But in this exersise we are used ansible play book for up the docker compose service.For running the nignx continer
  
## Ansible playbook

- The tool to run Ansible playbooks, which are a configuration and multinode deployment system. See the project home page (https://docs.ansible.com) for more information.

- In this this playbook we are used ansible modules for listed out the perameters and insturctions by ansible modules 

- In this case we writed one ansible playbook for up the docker compose services. we mentioned in `docker-compose.yml`file

- Playbook looks like below shown. 
```
---
- name: create nginx continer using docker compose
  become: yes
  hosts: localhost
  tasks:
    - name: docker-compose nginx deployment
      community.docker.docker_compose:
        project_src: /home/anil/anilpractce_git/ansible_learning/ansible-docker-compose-deployment/
        files:
          - docker-compose.yml
        state: present
```
- The ansible playbook is writes in ymal format and jason format. the above playbook used ymal format.

### Run Ansible playbook

- Ansible play book is run by using command `ansible-playbook playbook.yml`
- [click here](https://docs.ansible.com/ansible/latest/network/getting_started/first_playbook.html).

- when iam excute this command i got an error it looks like this 

![image](https://user-images.githubusercontent.com/97168620/205032968-ea59a867-a8e6-42a2-b86f-dedd26f83030.png)

- It shown a error belongs to python so download `pip install docker-py` it downloaded some packages.

-  And again run `ansible-playbook playbook.yml` command it shown error shown below
```
fatal: [localhost]: FAILED! => {"changed": false, "msg": "Unable to load docker-compose. Try pip install docker-compose. Error: Traceback (most recent call last):\n  File \"/tmp/ansible_community.docker.docker_compose_payload__xwpdm5e/ansible_community.docker.docker_compose_payload.zip/ansible_collections/community/docker/plugins/modules/docker_compose.py\", line 497, in <module>\nModuleNotFoundError: No module named 'compose'\n"}
```
- Download docker-compose module by executeing `pip3 install docker docker-compose` it will downloads the docker compse  python packages.

- After executing this command the ansible playbook up the docker compose service and `nginx` continer is runing .shown below 

```
anil@hellouser:~/anilpractce_git/ansible_learning/ansible-docker-compose-deployment$ sudo ansible-playbook playbook.yml
[WARNING]: provided hosts list is empty, only localhost is available. Note that the implicit localhost does not match 'all'

PLAY [create nginx continer using docker compose] ************************************************************************************************************

TASK [Gathering Facts] ***************************************************************************************************************************************
ok: [localhost]

TASK [docker-compose nginx deployment] ***********************************************************************************************************************
changed: [localhost]

PLAY RECAP ***************************************************************************************************************************************************
localhost                  : ok=2    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
```
### List out docker containers

- If you want to check the status or running containers in the docker engine/machine execute a command `docker ps` it will list out the all running containers.
```
anil@hellouser:~/anilpractce_git/ansible_learning/ansible-docker-compose-deployment$ sudo docker ps
CONTAINER ID   IMAGE          COMMAND                  CREATED          STATUS         PORTS                               NAMES
aaa98ca8c0f8   nginx:latest   "/docker-entrypoint.…"   18 minutes ago   Up 4 minutes   0.0.0.0:80->80/tcp, :::80->80/tcp   ansible-docker-compose-deployment_nginx_container_1
```
- The `nginx` container was running successfully.

> **Note** When you are using `pip` first of all know if it is present in your system if it is not in your system install by using
        `apt-get install pip3` command. And check the python,ansible and docker versions by using the tag ` --version`.

### Port-binding	

- A port binding is the configuration information that determines where and how a message will be sent or received. Depending on its type, a port binding might refer to physical locations, pipelines, or other orchestrations

- we are doing port binding in docker for the docker container communication to the outer world.

## Docker-port-binding  

- In this we are doing port binding for the `nginx_container` by providing the `-p` tag to the dockr run commnd 
```
docker run -p hostport:containerport <dockerimage>
```

- In this used shell script for binding the ports. 

- Make a directory named `docker-port-binding` by using the `mkdir` commmand . `cd` to the `docker-port-binding` folder and create a file named `script.sh` by using the `touch`command.

- inside of the `script.sh` write a command `docker run --name my-container -p 8000:80 -d nginx:latest` command for running the nginx container 

- Run the `script.sh` by using the `./script.sh` command it will runs the nginx container 

![image](https://user-images.githubusercontent.com/97168620/205053675-5bb3a147-b7e0-4a93-bbcf-57701dd5e5ab.png)

- Now check the container is acsessing out side world or not by using the `ip adress/localhost` and the port number `8000`.

![image](https://user-images.githubusercontent.com/97168620/205054508-3e1164d6-bf19-44f9-88c1-ed14d795c8c2.png)

- the container is accessing in web successfully 😃
     
## Docker-volume-mounting 	

- Volumes are the preferred mechanism for persisting data generated by and used by Docker containers. While bind mounts are dependent on the directory structure and OS of the host machine, volumes are completely managed by Docker. Volumes have several advantages over bind mounts.

- [click here](https://docs.docker.com/storage/volumes/) for more info

- In this task we are assigned volumes for `nginx_container` by using the tag `--mount type=bind source,target` for nignx container mounting volume by running the fallowing command 
```
docker run --name my-container --mount type=bind source=/tmp,target=/tmp -d busybox:latest

```
- 
