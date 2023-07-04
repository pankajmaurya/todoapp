## Pre requisites
Install Docker (some resources I referred to for Ubuntu 20 below)

https://docs.docker.com/engine/install/linux-postinstall/

https://www.digitalocean.com/community/tutorials/how-to-install-and-use-docker-on-ubuntu-20-04

__Docker installation had quirks, a reboot was needed as well__


## Verify your docker setup
```bash
docker run hello-world

docker compose version
```

## Installation

```bash
npm install -g @mindgrep/godspeed
npm install -g @devcontainers/cli
```

## Verify godspeed installation
```bash
godspeed help
```

## Creation of a project 
```bash

$ godspeed create todoappforgithub
                      _                                   _ 
   __ _    ___     __| |  ___   _ __     ___    ___    __| |
  / _` |  / _ \   / _` | / __| | '_ \   / _ \  / _ \  / _` |
 | (_| | | (_) | | (_| | \__ \ | |_) | |  __/ |  __/ | (_| |
  \__, |  \___/   \__,_| |___/ | .__/   \___|  \___|  \__,_|
  |___/                        |_|                           
projectDir:  [ '/home/pankaj/PortGodspeed/todoappforgithub' ]
Do you need mongodb? [y/n] [default: n] 
Do you need postgresdb? [y/n] [default: n] y
Please enter name of the postgres database [default: test] tododb
Please enter host port for postgres [default: 5432] 6666
Do you need kafka? [y/n] [default: n] 
Do you need elastisearch? [y/n] [default: n] 
Do you need redis? [y/n] [default: n] 
Please enter host port on which you want to run your service [default: 3000] 6000

```

### Optionally keeping project clean

Give the --noexamples option to create, check the help:

```bash
godspeed help create

```

## Gotchas in create and how to work around them

If you have another project running with a conflicting port for postgres or mongo, then the docker network creation will fail causing the godspeed project creation to fail. In such a case, one can cd into the project directory and edit the .godspeed file and fix the port. Run godspeed update then.


## Run the todo app service

Once the project is created, lets bring up the dev containers and run godspeed build and dev

```bash
devcontainer --workspace-folder . up
devcontainer --workspace-folder . exec godspeed build #on the mac I ran [devcontainer exec --workspace-folder . godspeed build]
devcontainer --workspace-folder . exec godspeed dev
```

## Use the service via curl

```bash
#Create a user
curl -s -X 'POST'   'http://localhost:4003/postgres/user'   -H 'accept: */*'   -H 'Content-Type: application/json'   -d '{
  "name": "Pankaj Maurya",
  "email": "foobar@gmail.com",
  "password": "string"
}' | jq

#output
#{
#  "id": 7,
#  "name": "Pankaj Maurya",
#  "email": "foobar@gmail.com",
#  "password": "string"
#}

#Create a todo
curl -s -X 'POST' \
  'http://localhost:4003/postgres/todo' \
  -H 'accept: */*' \
  -H 'Content-Type: application/json' \
  -d '{
  "title": "update the readme with curl commands",
  "completed": false,
  "createdAt": "2023-06-19T16:53:29.945Z",
  "updatedAt": "2023-06-19T16:53:29.945Z",
  "userId": 1
}' | jq

#output
#{
#  "id": 2,
#  "title": "update the readme with curl commands",
#  "completed": false,
#  "createdAt": "2023-06-19T16:53:29.945Z",
#  "updatedAt": "2023-06-19T16:53:29.945Z",
#  "userId": 1
#}

# mark todo as completed via put
curl -X 'PUT' \
  'http://localhost:4003/postgres/todo/1' \
  -H 'accept: */*' \
  -H 'Content-Type: application/json' \
  -d '{
  "completed": true
}'

# Note that the above does not produce output

# Search a todo which is completed
curl -X 'POST' \
  'http://localhost:4003/postgres/todo/search' \
  -H 'accept: */*' \
  -H 'Content-Type: application/json' \
  -d '{
  "where": {
    "completed": {
      "equals": true
    }
  }
}'

#output
#[
#  {
#    "id": 1,
#    "title": "update the readme with curl commands",
#    "completed": true,
#    "createdAt": "2023-06-19T16:53:29.945Z",
#    "updatedAt": "2023-06-19T16:56:57.221Z",
#    "userId": 1
#  }
#]

# Example of searching user
curl -X 'POST' \
  'http://localhost:4003/postgres/user/search' \
  -H 'accept: */*' \
  -H 'Content-Type: application/json' \
  -d '{
  "where": {
    "email": {
      "endsWith": "com"
    }
  }
}'

```

## Debugging

You may find it instructive to connect to the database configured.The configuration lives in the docker compose file.

Find values in .devcontainer/docker-compose.yaml, exec into the container and then start psql
docker exec -it 482539da31d1 /bin/bash
psql --user=postgres -p 5432 --dbname=userdb2

I eventually configured pgadmin:
https://www.tecmint.com/install-postgresql-and-pgadmin-in-ubuntu/
and added a connection to localhost:5555 (note the port) with the user as postgres,  password as postgres etc and found that the table is in public


## Docs and pointers
- https://docs.godspeed.systems/tutorial
- https://docs.godspeed.systems/docs/preface
- https://docs.godspeed.systems/docs/microservices/workflows
- https://www.prisma.io/docs/concepts/components/prisma-client/crud
- https://www.youtube.com/watch?v=zCLzvoSvF7E (Feature demonstration video by Ashutosh Tripathi)

## Whats next? 
Try following this README and contents of [chatgpt-prisma-gen](https://github.com/pankajmaurya/todoapp/tree/main/chatgpt-prisma-gen) folder to create more backends
