### Starter Dockerized React Nginx App 

&nbsp;
&nbsp;

Let's build a docker react app using nginx together. This app module should give us a good headstart for any project
we will be building in future on top.

## If you're already familiar with the npm modules that help us create this project
from scratch, you could just go ahead start by cloning this app
> git clone https://github.com/prabhakarBU/react-nginx-docker-app.git

## OR 

## If this is your first time or even if you just want to do this from scratch just to experience the process, Follow along the tutorial to create the project step by step.

Step 1: ( Let's get our Docker squared away! )
Let's download and install Docker using below link:
[https://www.docker.com/products/docker-desktop/]

Step 2: ( Let's go ahead and Create basic React app using command line and also just to make sure 
it'll work before deploying to Docker , we could run it once on the local machine before deploying it to the Docker )
` > npx create-react-app react-docker-boilerplate `
We should have package.json to build and run the project.
- Run ` > npm install ` to install all packages within node_modules folder that'll be added to your root directory.
- Run ` > npm run build ` to compile and build.
- Run ` > npm run start ` to start the app on port 3000.
- Go to any browser of your like and type the following URL -> http://localhost:3000

Step 3: ( Add Nginx )

Once we have made sure the project runs, let's start adding nginx as our proxy router.
Create a new folder 'nginx' to your root directory:

` > mkdir nginx `

Copy the contents of the "nginx" folder from this repository to the above newly created nginx folder.
Here's the content below for `nginx.conf` file :
```
server {

  listen 80;

  location / {
    root   /usr/share/nginx/html;
    index  index.html index.htm;
    try_files $uri /index.html;
  }

  error_page   500 502 503 504  /50x.html;

  location = /50x.html {
    root   /usr/share/nginx/html;
  }

}
```

Step 4: ( Add the Dockerfile )
Create a `Dockerfile` and a `.dockerignore` in your root folder and copy contents from this repository.

### Add the following content to Dockerfile if you want to do manually:
```
FROM node:12.18.1-alpine3.9 as build

WORKDIR /app
ENV PATH /app/node_modules/.bin:$PATH
COPY ./package.json /app/
RUN npm run --silent
RUN npm install
COPY . /app
RUN npm run build

FROM nginx:1.17.8-alpine
COPY --from=build /app/build /usr/share/nginx/html
RUN rm /etc/nginx/conf.d/default.conf
COPY nginx/nginx.conf /etc/nginx/conf.d
EXPOSE 80
CMD ["nginx", "-g", "daemon off;"]
```

### Add the following content to .dockeringnore:
```node_modules```

Step 5: ( Understand package.json )
Our `package.json` has all the dependencies our react app needs to install.
It also has build and run scripts to help our project create a build and run in dev and production mode.


Step 6: ( Run Docker commands to build image and Run container )
1. Build the Docker Image:
From the root of the app, run the following docker build command with tags:
` > docker build -f Dockerfile -t <some-image-name>:latest . `
Explaination:
-f is used to specify the filename. If we don't specify it, then we must rename our file to 'Dockerfile' and that's what the build command looks for in the current directory by default.
-t is used to tag the image. We can use this agrument to tag out image to make it distinct in any way we want to 
(Few examples could be : latest, dev, test, prod, v1.0.0 etc.)
. at the end is what actually tells docker to build using the current directory.

2. Run the Container from the above created image:
` > docker run -p 80:80 --rm <some-image-name>:latest `
Explaination:
-p to expose and bind ports. Here we are exposing port 80 of the container and binding it with port 80 of the host machine. The first one is of your machine (host OS) and the second one is of the docker image container. For example, if you use -p 8080:80 then you will need to go to http://localhost:8080 on your browser.
--rm to remove the container once it is stopped
`some-image-name:latest` is the name:tag combination of the image we want to build and run our container off of

3. Run the webapp:
Go to your browser and type: http://localhost:80

There you go! 
We have successfully dockerized our react web-app and hosted on our local machine.

