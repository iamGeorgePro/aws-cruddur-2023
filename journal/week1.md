# Week 1 — App Containerization

  - [Primary Homework](#week-1---app-containerization)
    + [Difference between RUN and CMD](#difference-between-run-and-cmd)
    + [Containerize Backend and Frontend](#containerize-backend-and-frontend)
    + [Updating the OpenAPI definitions](#updating-the-openapi-definitions)
    + [Updating the backend and frontend code to add notifications functionality](#updating-the-backend-and-frontend-code-to-add-notifications-functionality)
    + [DynamoDB Local and PostgreSQL](#dynamodb-local-and-postgresql)
  * [Homework Challenges](#homework-challenges)
    + [Run the Dockerfile CMD as an external script](#run-the-dockerfile-cmd-as-an-external-script)
    + [Pushing and tagging image to Docker Hub](#pushing-and-tagging-image-to-docker-hub)
    + [Multi-stage Build](#multi-stage-build)
    + [Implementing the healthcheck in docker-compose](#implementing-the-healthcheck-in-docker-compose)
    + [Docker Best Practices](#docker-best-practices)
    + [Install and run the code on the local machine](#install-and-run-the-code-on-the-local-machine)
    + [Running on an EC2 instance](#running-on-an-ec2-instance)

    ### Difference between RUN and CMD
`RUN` is a command we run to create a layer in the image / `CMD` is a command that the container is going to run when it starts up

### Containerize Backend and Frontend

#### Checked that the python works independent of Docker

```sh
cd backend-flask
export FRONTEND_URL="*"
export BACKEND_URL="*"
python3 -m flask run --host=0.0.0.0 --port=4567
cd ..
```

- port unlocked
- successfully opened port 4567
- got back JSON response

#### Docker Config
- Added Dockerfiles for backend and frontent and docker-compose.yml: [Link to commit 4771c99](https://github.com/iamGeorgePro/aws-bootcamp-cruddur-2023/commit/52dc97c2adfd3b41689463c71b2999ea2f51c815?diff=split)

#### building the container

```sh
docker build -t backend-flask ./backend-flask
```

#### Running the Container

Run 
```sh
docker run --rm -p 4567:4567 -it -e FRONTEND_URL='*' -e BACKEND_URL='*' backend-flask
```

Tried returning the container id into an Env Var
```sh
CONTAINER_ID=$(docker run --rm -p 4567:4567 -d backend-flask)
env | grep CONTAINER
```

#### Get Container Images or Running Container Ids

```
docker ps
docker images
```

#### Checked Container Logs

```sh
docker logs CONTAINER_ID -f
docker logs backend-flask -f
docker logs $CONTAINER_ID -f
```

#### Tried attaching shell to a Container

```sh
docker exec CONTAINER_ID -it /bin/bash
```

#### Added python and flask installation to gitpod.yml to avoid running it manually
[Link to commit  408e4e4]https://github.com/iamGeorgePro/aws-bootcamp-cruddur-2023/commit/408e4e4c690654c452f64095594affa50aaaeea0)

#### Three ways of running Docker compose
```sh
docker compose up
docker-compose up
#Right-click on the file in VSC and choose "Compose UP"
```

 ### Updating the backend and frontend code to add notifications functionality and Updating the OpenAPI definitions
[Link to OpenAPI files](https://github.com/iamGeorgePro/aws-bootcamp-cruddur-2023/blob/main/backend-flask/openapi-3.0.yml)


### DynamoDB Local and PostgreSQL

Modifying docker-compose to include databases: [Link to commit e8ac9a5 ](https://github.com/iamGeorgePro/aws-bootcamp-cruddur-2023/commit/e8ac9a5f7248ceb99678d36b0c681c0ae5c50ba0)

Screenshot of Postgres working:
![CleanShot 2023-02-24 at 08 50 14](https://user-images.githubusercontent.com/10653195/221122516-48edec2c-30d5-4e6a-b951-93cedf675493.png)

Can see the standard tables:

![CleanShot 2023-02-24 at 08 52 29](https://user-images.githubusercontent.com/10653195/221122842-8f5c152e-1141-48e8-9579-64c00f5a9a40.png)

Tried connecting via CLI client:
```sh
sudo apt-get install -y postgresql-client
psql -h localhost -U postgres
```
![Proof of postgres database](assets/postgress.png)

## Homework Challenges
### Run the Dockerfile CMD as an external script
1. Created `run_flask.sh`
```bash
#!/bin/bash
python3 -m flash run --host=0.0.0.0 --port=4567
```
2. Built the container
```sh
docker build -t backend-flask ./backend-flask
```
3. Run the container
```sh
docker run -d --rm -p 4567:4567 -it -e FRONTEND_URL='*' -e BACKEND_URL='*' backend-flask
```

#### Log
```sh
gitpod /workspace/aws-bootcamp-cruddur-2023 (main) $ docker build -t backend-flask ./backend-flask

Sending build context to Docker daemon  60.93kB
Step 1/10 : FROM python:3.10-slim-buster
 ---> b5d627f77479
Step 2/10 : WORKDIR /backend-flask
 ---> Using cache
 ---> dab5faf8aaa4
Step 3/10 : COPY requirements.txt requirements.txt
 ---> Using cache
 ---> 23f30b13d8ec
Step 4/10 : RUN pip3 install -r requirements.txt
 ---> Using cache
 ---> d940517a0a09
Step 5/10 : COPY . .
 ---> Using cache
 ---> 20edee9a7fde
Step 6/10 : COPY run_flask.sh /usr/local/bin/
 ---> Using cache
 ---> dcd90cd3e728
Step 7/10 : RUN chmod +x /usr/local/bin/run_flask.sh
 ---> Using cache
 ---> aa1211fad82a
Step 8/10 : ENV FLASK_ENV=development
 ---> Using cache
 ---> ee3b5416f8ea
Step 9/10 : EXPOSE ${PORT}
 ---> Using cache
 ---> ca7136f2cb16
Step 10/10 : CMD [ "/usr/local/bin/run_flask.sh"]
 ---> Using cache
 ---> 2de9bf49876f
Successfully built 2de9bf49876f
Successfully tagged backend-flask:latest
gitpod /workspace/aws-bootcamp-cruddur-2023 (main) $ docker run --rm -p 4567:4567 -it -e FRONTEND_URL='*' -e BACKEND_URL='*' backend-flask
'FLASK_ENV' is deprecated and will not be used in Flask 2.3. Use 'FLASK_DEBUG' instead.
'FLASK_ENV' is deprecated and will not be used in Flask 2.3. Use 'FLASK_DEBUG' instead.
'FLASK_ENV' is deprecated and will not be used in Flask 2.3. Use 'FLASK_DEBUG' instead.
 * Debug mode: on
WARNING: This is a development server. Do not use it in a production deployment. Use a production WSGI server instead.
 * Running on all addresses (0.0.0.0)
 * Running on http://127.0.0.1:4567
 * Running on http://172.17.0.2:4567
Press CTRL+C to quit
 * Restarting with stat
'FLASK_ENV' is deprecated and will not be used in Flask 2.3. Use 'FLASK_DEBUG' instead.
'FLASK_ENV' is deprecated and will not be used in Flask 2.3. Use 'FLASK_DEBUG' instead.
'FLASK_ENV' is deprecated and will not be used in Flask 2.3. Use 'FLASK_DEBUG' instead.
 * Debugger is active!
 * Debugger PIN: 113-469-523

gitpod /workspace/aws-bootcamp-cruddur-2023 (main) $ docker run -d --rm -p 4567:4567 -it -e FRONTEND_URL='*' -e BACKEND_URL='*' backend-flask
66c58f329e6f0f7f85ee5ce7939b38f940d7b5b263461296173fcd38bb4fe4cf

gitpod /workspace/aws-bootcamp-cruddur-2023 (main) $ curl https://4567-nickda-awsbootcampcrudd-su9p9lpj1ju.ws-eu87.gitpod.io/api/activities/home

[
  {
    "created_at": "2023-02-22T09:32:27.981983+00:00",
    "expires_at": "2023-03-01T09:32:27.981983+00:00",
    "handle": "Andrew Brown",
    "likes_count": 5,
    "message": "Cloud is fun!",
    "replies": [
      {
        "created_at": "2023-02-22T09:32:27.981983+00:00",
... edited for brevity
```

### Pushing and tagging image to Docker Hub
#### 1. Log in to Docker Hub
```sh
docker login
```
##### Log
```
Login with your Docker ID to push and pull images from Docker Hub. If you don't have a Docker ID, head over to https://hub.docker.com to create one.
Username: *REDACTED*
Password: *REDACTED*
WARNING! Your password will be stored unencrypted in /home/gitpod/.docker/config.json.
Configure a credential helper to remove this warning. See
https://docs.docker.com/engine/reference/commandline/login/#credentials-store
```
**WARNING! Your password will be stored unencrypted in /home/gitpod/.docker/config.json.** this looks like a potential problem. Remember to do `docker logout` in the end to remove the credentials from the `config.json`

#### 2. Tag the image
```sh
docker tag aws-bootcamp-cruddur-2023-backend-flask georgepro1/cruddur-backend-flask:v1
```

#### 3. Push the image to Docker Hub
```sh
docker push georgepro1/cruddur-backend-flask:v1
```
##### Log
```
he push refers to repository [docker.io/nickda/cruddur-backend-flask]
0228ec85ec2a: Pushed 
97f998ea0f33: Pushed 
cb35014d1c7c: Pushed 
9aa90f80bdc0: Pushed 
5e8ed28e644d: Pushed 
66a7b77b1ced: Pushed 
223e3b83550e: Pushed 
53b2529dfca9: Mounted from library/python 
5be8f6899d42: Pushed 
8d60832b730a: Pushed 
63b3cf45ece8: Pushed 
v1: digest: sha256:cbc2104978a95dfca63e0776f18bb9e3c9648d0270a7a434e959d2803b3b2874 size: 2203
```

#### 4. Verify in GUI that the image was indeed pushed
![CleanShot 2023-02-24 at 11 07 00](assets/dockerhub.png)


#### 5. Deleting the Docker Hub credentials from the ~/.docker/config.json
```sh
docker logout
```
##### Log
```
Removing login credentials for https://index.docker.io/v1/
```

### Multi-stage Build
[link to commit]()

MSB has the potential of making our container images smaller.
To determine whether that is the case let's check the size of our non-multi-stage container:

```sh
docker images backend-flask:latest
REPOSITORY      TAG       IMAGE ID       CREATED          SIZE
backend-flask   latest    2cb00c11d19e   13 minutes ago   276MB <--- image size is here
```
#### Stage 1 - Building the application
In the first stage, the application is built by installing the required Python packages specified in requirements.txt. The --user flag is used to install packages in the local user's home directory instead of the system directory, and the --no-cache-dir flag is used to avoid caching the installed packages, which helps to reduce the final image size.

```dockerfile
FROM python:3.10-slim-buster AS builder

WORKDIR /backend-flask

COPY requirements.txt requirements.txt
RUN pip3 install --user --no-cache-dir -r requirements.txt

COPY . .
```
#### Stage 2 - Running the application
In the second stage, the built application is copied from the first stage using the --from=builder flag, along with the Python packages installed in the first stage. The PATH environment variable is set to include the local user's home directory, so that the installed packages can be found when the application is run.

```dockerfile
FROM python:3.10-slim-buster

WORKDIR /backend-flask

COPY --from=builder /root/.local /root/.local
ENV PATH=/root/.local/bin:$PATH

COPY run_flask.sh /usr/local/bin/
RUN chmod +x /usr/local/bin/run_flask.sh

ENV FLASK_ENV=development

EXPOSE ${PORT}

CMD [ "/usr/local/bin/run_flask.sh"]
```

#### After building the image we check the size again
```sh
docker images backend-flask:latest
REPOSITORY      TAG       IMAGE ID       CREATED          SIZE
backend-flask   latest    2f68bf882cf0   16 seconds ago   223MB <--- woow! We saved Gitpod 53MB of storage
```

### Implementing the healthcheck in docker-compose
#### Adding a healthcheck to docker-compose.yml

[link to commit]()
```yml
services:
  backend-flask:
    environment:
      FRONTEND_URL: "https://3000-${GITPOD_WORKSPACE_ID}.${GITPOD_WORKSPACE_CLUSTER_HOST}"
      BACKEND_URL: "https://4567-${GITPOD_WORKSPACE_ID}.${GITPOD_WORKSPACE_CLUSTER_HOST}"
    build: ./backend-flask
    ports:
      - "4567:4567"
    volumes:
      - ./backend-flask:/backend-flask
# Healtcheck code begins here      
    healthcheck:
      test: curl --fail https://4567-${GITPOD_WORKSPACE_ID}.${GITPOD_WORKSPACE_CLUSTER_HOST}/api/activities/home
      interval: 30s
      timeout: 10s
      retries: 3
```



>Additionally, if using curl for healthcheck it needs to be installed into the container. Hence, the Dockerfile update:
```dockerfile
RUN apt-get update 
RUN apt-get install -y gcc
RUN apt-get install -y curl
```

##### Verification
Pay attention to the STATUS column in the table:
```
CONTAINER ID   IMAGE                                         COMMAND                  CREATED              STATUS                    PORTS                                       NAMES
1249cea05406   aws-bootcamp-cruddur-2023-frontend-react-js   "docker-entrypoint.s…"   32 seconds ago       Up 31 seconds             0.0.0.0:3000->3000/tcp, :::3000->3000/tcp   aws-bootcamp-cruddur-2023-frontend-react-js-1
1501d438d5c0   aws-bootcamp-cruddur-2023-backend-flask       "python3 -m flask ru…"   33 seconds ago       Up 31 seconds (healthy)   0.0.0.0:4567->4567/tcp, :::4567->4567/tcp   aws-bootcamp-cruddur-2023-backend-flask-1
bb6bb619e3be   postgres:13-alpine                            "docker-entrypoint.s…"   About a minute ago   Up About a minute         0.0.0.0:5432->5432/tcp, :::5432->5432/tcp   aws-bootcamp-cruddur-2023-db-1
771a0fb469fe   amazon/dynamodb-local:latest                  "java -jar DynamoDBL…"   About a minute ago   Up About a minute         0.0.0.0:8000->8000/tcp, :::8000->8000/tcp   dynamodb-local
```

If the healthcheck didn't pass it shows up as (unhealthy) in `docker ps`:
```
CONTAINER ID   IMAGE                                         COMMAND                  CREATED              STATUS                          PORTS                                       NAMES
d9526a8c74c9   aws-bootcamp-cruddur-2023-backend-flask       "python3 -m flask ru…"   About a minute ago   Up About a minute (unhealthy)   0.0.0.0:4567->4567/tcp, :::4567->4567/tcp   aws-bootcamp-cruddur-2023-backend-flask-1
```
Also, the exclamation mark appears in the Docker extension for VSC:

![CleanShot 2023-02-24 at 13 44 26]()

### Docker Best Practices

[x] Multi-stage build
[x] Put layers that are likely to change as low as possible in the Dockerfile
[x] Combined `RUN apt-get update` and `RUN apt-get install` thus creating a single layer which reduces the image file. Furthermore, added `--no-install-recommends` to avoid installing recommended packages and pinned the versions of the packages which I'm installing.
before:
```dockerfile
RUN apt-get update 
RUN apt-get install -y gcc
RUN apt-get install -y curl
```

after:
```dockerfile
RUN apt-get update && apt-get install -y --no-install-recommends \
    gcc=9.3.0 \
    curl=7.68.0 \
    && rm -rf /var/lib/apt/lists/*
```
>RUN, COPY, and ADD add size to the image. Combine them where possible
>With multi-stage builds, don't worry too much about overly optimizing the commands in temp stages.
[x] Added --no-cache-dir to pip command to not use the pip cache directory. This can save disk space.
[x] Installed hadolint and hadolint extension for VScode to lint the Dockerfile code. Also added the extension and installation to .gitpod.yml

### Install and run the code on the local machine
#### Installed Docker on Desktop
![CleanShot 2023-02-25 at 09 51 38]()
#### Cloned the cruddur repo locally and created a branch to avoid impacting gitpod runs
![CleanShot 2023-02-25 at 09 54 04]()
```sh
cd frontend-react-js
npm install
```
#### Changes to run locally
The URLs for frontend and backend had to be changed to local versions to work around GitPod magic where it appends the port at the front and wraps it in TLS.

```yml
version: "3.8"
services:
  backend-flask:
    environment:
      FRONTEND_URL: "http://localhost:3000"  <-----
      BACKEND_URL: "http://localhost:4567" <-----
    build: ./backend-flask
    ports:
      - "4567:4567"
    volumes:
      - ./backend-flask:/backend-flask
    healthcheck:
      test: curl --fail http://localhost:4567/api/activities/home  <-----
      interval: 30s
      timeout: 10s
      retries: 3


  frontend-react-js:
    environment:
      REACT_APP_BACKEND_URL: "http://localhost:4567"  <-----
    build: ./frontend-react-js
```

### Running on an EC2 instance

#### Creating the instance with SSM support
Using Terraform for this
[link to commit]()

```sh
terraform init
terraform fmt
terraform validate
terraform plan
```
##### Log
```hcl
Terraform used the selected providers to generate the following execution plan. Resource actions are indicated with the following symbols:
  + create

Terraform will perform the following actions:

  # aws_instance.ec2_instance will be created
  + resource "aws_instance" "ec2_instance" {
      + ami                                  = "ami-0ae87df03b4940655"
      + arn                                  = (known after apply)
      + associate_public_ip_address          = true
      + availability_zone                    = (known after apply)
      + cpu_core_count                       = (known after apply)
      + cpu_threads_per_core                 = (known after apply)
      + disable_api_stop                     = (known after apply)
      + disable_api_termination              = (known after apply)
      + ebs_optimized                        = (known after apply)
      + get_password_data                    = false
      + host_id                              = (known after apply)
      + host_resource_group_arn              = (known after apply)
      + iam_instance_profile                 = (known after apply)
      + id                                   = (known after apply)
      + instance_initiated_shutdown_behavior = (known after apply)
      + instance_state                       = (known after apply)
      + instance_type                        = "t3.micro"
      + ipv6_address_count                   = (known after apply)
      + ipv6_addresses                       = (known after apply)
      + key_name                             = "use1.pem"
      + monitoring                           = (known after apply)
      + outpost_arn                          = (known after apply)
      + password_data                        = (known after apply)
      + placement_group                      = (known after apply)
      + placement_partition_number           = (known after apply)
      + primary_network_interface_id         = (known after apply)
      + private_dns                          = (known after apply)
      + private_ip                           = (known after apply)
      + public_dns                           = (known after apply)
      + public_ip                            = (known after apply)
      + secondary_private_ips                = (known after apply)
      + security_groups                      = (known after apply)
      + source_dest_check                    = true
      + subnet_id                            = "subnet-044dfba335b22ce90"
      + tags                                 = {
          + "Name" = "Docker test"
        }
      + tags_all                             = {
          + "Name" = "Docker test"
        }
      + tenancy                              = (known after apply)
      + user_data                            = (known after apply)
      + user_data_base64                     = (known after apply)
      + user_data_replace_on_change          = false
      + vpc_security_group_ids               = (known after apply)

      + capacity_reservation_specification {
          + capacity_reservation_preference = (known after apply)

          + capacity_reservation_target {
              + capacity_reservation_id                 = (known after apply)
              + capacity_reservation_resource_group_arn = (known after apply)
            }
        }

      + ebs_block_device {
          + delete_on_termination = (known after apply)
          + device_name           = (known after apply)
          + encrypted             = (known after apply)
          + iops                  = (known after apply)
          + kms_key_id            = (known after apply)
          + snapshot_id           = (known after apply)
          + tags                  = (known after apply)
          + throughput            = (known after apply)
          + volume_id             = (known after apply)
          + volume_size           = (known after apply)
          + volume_type           = (known after apply)
        }

      + enclave_options {
          + enabled = (known after apply)
        }

      + ephemeral_block_device {
          + device_name  = (known after apply)
          + no_device    = (known after apply)
          + virtual_name = (known after apply)
        }

      + maintenance_options {
          + auto_recovery = (known after apply)
        }

      + metadata_options {
          + http_endpoint               = (known after apply)
          + http_put_response_hop_limit = (known after apply)
          + http_tokens                 = (known after apply)
          + instance_metadata_tags      = (known after apply)
        }

      + network_interface {
          + delete_on_termination = (known after apply)
          + device_index          = (known after apply)
          + network_card_index    = (known after apply)
          + network_interface_id  = (known after apply)
        }

      + private_dns_name_options {
          + enable_resource_name_dns_a_record    = (known after apply)
          + enable_resource_name_dns_aaaa_record = (known after apply)
          + hostname_type                        = (known after apply)
        }

      + root_block_device {
          + delete_on_termination = (known after apply)
          + device_name           = (known after apply)
          + encrypted             = (known after apply)
          + iops                  = (known after apply)
          + kms_key_id            = (known after apply)
          + tags                  = (known after apply)
          + throughput            = (known after apply)
          + volume_id             = (known after apply)
          + volume_size           = (known after apply)
          + volume_type           = (known after apply)
        }
    }

  # module.ssm_instance_profile.aws_iam_instance_profile.this will be created
  + resource "aws_iam_instance_profile" "this" {
      + arn         = (known after apply)
      + create_date = (known after apply)
      + id          = (known after apply)
      + name        = (known after apply)
      + path        = "/"
      + role        = (known after apply)
      + tags_all    = (known after apply)
      + unique_id   = (known after apply)
    }

  # module.ssm_instance_profile.aws_iam_role.this will be created
  + resource "aws_iam_role" "this" {
      + arn                   = (known after apply)
      + assume_role_policy    = jsonencode(
            {
              + Statement = {
                  + Action    = "sts:AssumeRole"
                  + Effect    = "Allow"
                  + Principal = {
                      + Service = "ec2.amazonaws.com"
                    }
                }
              + Version   = "2012-10-17"
            }
        )
      + create_date           = (known after apply)
      + force_detach_policies = false
      + id                    = (known after apply)
      + managed_policy_arns   = (known after apply)
      + max_session_duration  = 3600
      + name                  = (known after apply)
      + name_prefix           = (known after apply)
      + path                  = "/"
      + tags_all              = (known after apply)
      + unique_id             = (known after apply)

      + inline_policy {
          + name   = (known after apply)
          + policy = (known after apply)
        }
    }

  # module.ssm_instance_profile.aws_iam_role_policy_attachment.this will be created
  + resource "aws_iam_role_policy_attachment" "this" {
      + id         = (known after apply)
      + policy_arn = "arn:aws:iam::aws:policy/AmazonSSMManagedInstanceCore"
      + role       = (known after apply)
    }

  # module.ssm_instance_profile.random_string.this[0] will be created
  + resource "random_string" "this" {
      + id          = (known after apply)
      + length      = 3
      + lower       = true
      + min_lower   = 0
      + min_numeric = 0
      + min_special = 0
      + min_upper   = 0
      + number      = true
      + numeric     = true
      + result      = (known after apply)
      + special     = false
      + upper       = false
    }

Plan: 5 to add, 0 to change, 0 to destroy.

#### Terraform Apply
```sh
terraform apply
```
##### Log
```hcl
module.ssm_instance_profile.random_string.this[0]: Creating...
module.ssm_instance_profile.random_string.this[0]: Creation complete after 0s [id=zri]
module.ssm_instance_profile.aws_iam_role.this: Creating...
module.ssm_instance_profile.aws_iam_role.this: Creation complete after 1s [id=ssm-role-zri]
module.ssm_instance_profile.aws_iam_role_policy_attachment.this: Creating...
module.ssm_instance_profile.aws_iam_instance_profile.this: Creating...
module.ssm_instance_profile.aws_iam_role_policy_attachment.this: Creation complete after 0s [id=ssm-role-zri-20230225094729817500000001]
module.ssm_instance_profile.aws_iam_instance_profile.this: Creation complete after 0s [id=ssm-instance-profile-zri]
aws_instance.ec2_instance: Creating...
aws_instance.ec2_instance: Still creating... [10s elapsed]
aws_instance.ec2_instance: Still creating... [20s elapsed]
aws_instance.ec2_instance: Still creating... [30s elapsed]
aws_instance.ec2_instance: Still creating... [40s elapsed]
aws_instance.ec2_instance: Still creating... [50s elapsed]
aws_instance.ec2_instance: Creation complete after 55s [id=i-095a34a4377d993f0]

Apply complete! Resources: 5 added, 0 changed, 0 destroyed.
```

#### Instance in the Console
![CleanShot 2023-02-25 at 10 50 21]()

#### SSM Access to the Instance
![CleanShot 2023-02-25 at 10 52 44]()

#### Cloning the repo and installing docker
```sh
sudo snap install docker
```

#### Tagging and pushing the latest version of container
```sh
docker login
docker tag aws-bootcamp-cruddur-2023-backend-flask nickda/cruddur-backend-flask:2.0
docker push nickda/cruddur-backend-flask:2.0
```

#### Pulling and running on an EC2 instance
```sh
docker pull 
```
![CleanShot 2023-02-25 at 11 04 47]()
```sh
docker run --rm -p 4567:4567 -d 
```

**Solution**: rebuilding the image on GitPod:

```sh
docker login
docker tag aws-bootcamp-cruddur-2023-backend-flask 
docker push 
```

![CleanShot 2023-02-25 at 11 18 18]()

