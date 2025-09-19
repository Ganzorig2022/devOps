### Docker containers and images

> Images are Read-Only templates used to create containers.

Docker Instructions

```yaml
FROM <name_of_image>
FROM pyhton:3.10

# COPY - copies files or directories from source to destination.
COPY <source> <destination>
# Copy files/folders to the main folder od the container
COPY . .
RUN - runs a command within a container
RUN <command>
RUN npm install

ENTRYPOINT - defines container’s default behavior
ENTRYPOINT  [“command”, “argument”]
ENTRYPOINT  [“python”, “hello_world.py”]
```

Docker build command

builds docker image from a dockerfile
dockerfile needs to be located in build’s context

```bash
docker build <context>
```

```Dockerfile
FROM node

WORKDIR /app

COPY . /app # eveything in current directory to /app in container
RUN npm install
CMD ["npm", "run", "start_production"] # command to run when container starts using script from package.json
```

```Dockerfile
FROM node:22-alpine AS builder
RUN apk update && apk upgrade && \
    apk add --no-cache bash git openssh
WORKDIR /usr/src/app
COPY ./package* ./
RUN npm install --loglevel verbose
COPY . .
RUN npm run build

FROM node:22-alpine
RUN apk update && apk upgrade && \
    apk add --no-cache bash git openssh
WORKDIR /usr/src/app
COPY ./package* ./
RUN npm install --omit=dev
COPY --from=builder /usr/src/app/dist ./dist
CMD ["npm", "run", "start_production"]
```

```bash
# 1) docker build  - Build image from Dockerfile
# 2) --network=host - Use the host's networking stack inside the container
# 3) --add-host=git.qpay.mn:192.168.100.47 - Add a custom host-to-IP mapping in the container's /etc/hosts file
# 4) -t git.qpay.mn:5005/merchant/sms-admin-service:dev - Tag the image with a name and version. (git.qpay.mn:5005/ - company's Docker repo server, merchant/sms-admin-service - image name, dev - version tag)
# 5) -f deployment/Dockerfile - Specify the path to the Dockerfile to use for building the image
# 6) . - The build context, which is the current directory in this case

docker build --network=host --add-host=git.qpay.mn:192.168.100.47 -t git.qpay.mn:5005/merchant/sms-admin-service:dev -f deployment/Dockerfile .
```
