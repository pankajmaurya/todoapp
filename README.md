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
devcontainer --workspace-folder . exec godspeed build
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

```

## Docs and pointers
- https://docs.godspeed.systems/tutorial
- https://docs.godspeed.systems/docs/preface

