# Cloud Computing

### Made by: Ansh Raiyani (21BCP391)

## Problem Statement:

Create any three-tier application using Docker, using a multi-container setup. Build at least one docker image using Dockerfile. You are free to use your old projects, build new projects or take any project from GitHub. However, if you are using a project from Github, properly cite the original author of the code in your blog.

## Steps

1. Get a sample 3-tier project. <br>
   Here, I have cloned this MERN stack project from github.
   `https://github.com/samaronybarros/movies-app`
   ![image info](./ss/1.png)

2. After cloning, we have to create two separate `Dockerfile` for each `client` & `server`. <br>
   We wil use `docker compose` to create a multi-container setup.

3. Add the below code in `client/Dockerfile`

   ```Dockerfile
   FROM node:14-slim
   WORKDIR /usr/src/app
   COPY ./package.json ./
   COPY ./yarn.lock ./
   RUN yarn install
   COPY . .
   EXPOSE 3000
   CMD [ "yarn", "start" ]
   ```

   <b>NOTE:</b> remove "PORT=8000" section from start script in package.json. <br>
   it should look something like this:

   ```json
   "scripts": {
   "start": "react-scripts start",
   "build": "react-scripts build",
   "test": "react-scripts test",
   "eject": "react-scripts eject"
   },
   ```

4. Similarly, create a Dockerfile in `server/Dockerfile`

   ```Dockerfile
   FROM node:14-slim
   WORKDIR /usr/src/app
   COPY ./package.json ./
   COPY ./yarn.lock ./
   RUN yarn install
   COPY . .
   EXPOSE 5000
   CMD [ "index.js" ]
   ```

   <b>NOTE:</b> change the port number in `server/index.js`

   ```js
   const apiPort = 5000;
   ```

5. create a `docker-compose.yml` in the root directory of the project.

   ```docker
   version: "3"
   services:
   react-app:
       image: react-app
       stdin_open: true
       ports:
       - "3000:3000"
       networks:
       - mern-app
   api-server:
       image: api-server
       ports:
       - "5000:5000"
       networks:
       - mern-app
       depends_on:
       - mongo
   mongo:
       image: mongo
       ports:
       - "27017:27017"
       networks:
       - mern-app
       volumes:
       - mongo-data:/data/db
   networks:
   mern-app:
       driver: bridge
   volumes:
   mongo-data:
       driver: local
   ```

6. Add changes to the following files:

   1. `client/src/api/index.js`
      ```js
      const api = axios.create({
        baseURL: "http://localhost:5000/api",
      });
      ```
   2. `server/db/index.js` <br>
      instead of localhost, use mongo in the connection string as we as made a service named mongo in docker-compose.yml.
      ```js
      const connectionString = "mongodb://localhost:27017/new-cinema";
      ```

7. Open terminal in the root directory of your folder and run these docker commands: <br>

   1. ```bash
      docker build -t "react-app" ./client/
      ```

      ![image info](./ss/2.png)
      docker desktop:
      ![image info](./ss/3.png)

   2. ```bash
      docker build -t "api-server" ./server/
      ```

      ![image info](./ss/4.png)
      docker desktop:
      ![image info](./ss/5.png)

   3. ```bash
      docker pull mongo:latest
      ```
      ![image info](./ss/6.png)

8. Now we use Docker Compose to run stack of containers.

   ```bash
   docker-compose up -d
   ```

   ![image info](./ss/7.png)<br>

   <b>Docker Desktop</b>
   ![image info](./ss/8.png)

   ![image info](./ss/9.png)

   ![image info](./ss/10.png)

   ![image info](./ss/11.png)

   ![image info](./ss/12.png)

9. Test on localhost.
   ![image info](./ss/13.png)

   ![image info](./ss/14.png)

   ![image info](./ss/15.png)

10. Verify on MongoDB Compass
    ![image info](./ss/16.png)

11. Push Images to DockerHub
    ![image info](./ss/17.png)

    ![image info](./ss/18.png)

    ![image info](./ss/19.png)
