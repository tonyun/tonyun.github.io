---
title: How to deploy a simple Node.js+MongoDB app
---
### Clone a Node.js application from GitHub

First, clone from the following URL to Ubuntu VM:

https://github.com/potikanond/docker-node-sample3.git

On Ubuntu VM run the following command:

`
$ git clone https://github.com/potikanond/docker-node-sample3.git
`

This will clone the application from GitHub to `docker-node-sample3` directory.

### Review the Node.js application

package.json:
```json
{
  "name": "docker-node-mongo",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "scripts": {
    "start": "node index.js"
  },
  "keywords": [],
  "author": "",
  "license": "ISC",
  "dependencies": {
    "body-parser": "^1.18.3",
    "ejs": "^2.6.1",
    "express": "^4.16.3",
    "mongoose": "^5.2.7"
  }
}
```

**Dependencies:**
- *express* : a Node.js framework for web application (and api) development.
- *ejs* : Embedded JavaScript template is one of a Node.js template engines for generate HTML using JavaScript.
- *body-parser* : a middleware for Node.js, used to parse incoming web request, help extracting passing parameters.
- *mongoose* : a library for Node.js to work with MongoDB.


### Start the Node.js application right way? 

The answer is **NOT** at this step. **This application stores and retrieves data on MongoDB, therefore, we need to configure MongoDB first.**

But if you really want to start the Node.js application, we need to install **Node.js** on the Ubuntu VM. After that, go into the `docker-node-sample3` directory and enter the following command:

`
$ npm start
`

If we do not have MongoDB then this would give us an **ERROR**:

<pre>
$ npm start

> docker-node-mongo@1.0.0 start /Users/dpotikan/workspace/tutorial-docker/docker-node-sample3
> node index.js

Server running...
{ MongoNetworkError: failed to connect to server [mongo:27017] on first connect 
  [MongoNetworkError: getaddrinfo ENOTFOUND mongo mongo:27017] ... }
</pre>

With Docker, we will NOT install Node.js on the docker host but we will create a Node.js container and run the application inside.

### Create a docker image for the Node.js application

First, create a `Dockerfile` inside the `docker-node-sample3` directory.

Dockerfile:
<pre>
FROM node:10-alpine

WORKDIR /usr/src/app

COPY package*.json ./

RUN npm install

COPY . .

EXPOSE 3000

CMD ["npm", "start"]
</pre>

For more detail on **dockerizing a Node.js web app**, see the following link:
https://nodejs.org/en/docs/guides/nodejs-docker-webapp/

### Create `.dockerignore` file in the same directory as the `Dockerfile`

.dockerignore:
<pre>
node_modules
npm-debug.log
</pre>

This will prevent `local_modules` and `debug logs` from being copied onto your Docker image and overwriting modules installed within your image.


### Building your Node.js docker image

Use the `docker image build ... ` command to build and tag image.

<pre>
$ docker image build -t cpe405/example33 .
Sending build context to Docker daemon  148.5kB
Step 1/7 : FROM node:10-alpine
 ---> 4e50ad7c0e0b
Step 2/7 : WORKDIR /usr/src/app
 ---> Running in 5103a44253d3
Removing intermediate container 5103a44253d3
 ---> 792d79e04737
Step 3/7 : COPY package*.json ./
 ---> b2e4d3253e7f
Step 4/7 : RUN npm install
 ---> Running in 5c75c3eb9927
npm WARN docker-node-mongo@1.0.0 No description
npm WARN docker-node-mongo@1.0.0 No repository field.

added 80 packages from 62 contributors and audited 181 packages in 6.483s
found 0 vulnerabilities

Removing intermediate container 5c75c3eb9927
 ---> 0bd134e0855a
Step 5/7 : COPY . .
 ---> da43997f294f
Step 6/7 : EXPOSE 3000
 ---> Running in 7467dab066a3
Removing intermediate container 7467dab066a3
 ---> 23348db6b2bc
Step 7/7 : CMD [ "npm", "start" ]
 ---> Running in 15a2309daf6f
Removing intermediate container 15a2309daf6f
 ---> 5e97c2c283d5
Successfully built 5e97c2c283d5
Successfully tagged cpe405/example33:latest
</pre>

If we would create and start a container from this image without MongoDB, there would be **ERROR** as shown before.

<pre>
$ docker container run -p 8080:3000 cpe405/example33

> docker-node-mongo@1.0.0 start /usr/src/app
> node index.js

Server running...
{ MongoNetworkError: failed to connect to server [mongo:27017] on first connect [MongoNetworkError: getaddrinfo ENOTFOUND mongo mongo:27017] ... }
</pre>

### Start the application properly

To start the application properly. First, start the MongoDB container:

`
$ docker container run -d --name my_mongo -p 27017:27017 mongo:4.0.1-xenial
`

We will use the mongo container's name, `my_mongo`, for linking with the Node.js container.
Next, start the Node.js container (in foreground) and **link** to the MongoDB container.

<pre>
$ docker container run -p 8080:3000 --link my_mongo:mongo cpe405/example33

> docker-node-mongo@1.0.0 start /usr/src/app
> node index.js

Server running...
MongoDB Connected
</pre>

The `--link my_mongo:mongo` option tells docker to map the MongoDB container's name, `my_mongo`, as the name `mongo` for the Node.js container. If we take a look in the `index.js` file, we will see that `mongoose` use the name `mongo` to refer to MongoDB container.

index.js:
```javascript
...
mongoose
  .connect(
    'mongodb://mongo:27017/docker-node-mongo',
    { useNewUrlParser: true }
  )
  .then(() => console.log('MongoDB Connected'))
  .catch(err => console.log(err));
...
```

Now, we can access the Node.js app by *http://localhost:8080*

### Summary
To deploy the Node.js app, we need to manually start a MongoDB container first then the Node.js app container.
Next we will learn how to use **docker-compose** tool to deploy the app.
