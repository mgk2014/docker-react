3-22-2020

Due to a recent update in the Create React App library, we will need to change how we start our containers.

In the upcoming lecture, you'll need to add the -it flag to run the container in interactive mode:

docker run -it -p 3000:3000 IMAGE_ID 


4-1-2020

Recently, a bug was introduced with the latest Create React App version that is causing the React app to exit when starting with Docker Compose.

To Resolve this:

Add stdin_open property to your docker-compose.yml file

      web:
        stdin_open: true

Make sure you rebuild your containers after making this change with  docker-compose down && docker-compose up --build

https://github.com/facebook/create-react-app/issues/8688

https://stackoverflow.com/questions/60790696/react-scripts-start-exiting-in-docker-foreground-cmd


Named Builders and AWS

updated 10-1-2020

In the next lecture, we will be creating a multi-step build in our production Dockerfile. AWS currently will fail if you attempt to use a named builder as shown.

To remedy this, we should create an unnamed builder like so:

Instead of this:

    FROM node:alpine as builder
    WORKDIR '/app'
    COPY package.json .
    RUN npm install
    COPY . .
    RUN npm run build
     
    FROM nginx
    COPY --from=builder /app/build /usr/share/nginx/html

Do this:

    FROM node:alpine
    WORKDIR '/app'
    COPY package.json .
    RUN npm install
    COPY . .
    RUN npm run build
     
    FROM nginx
    COPY --from=0 /app/build /usr/share/nginx/html

Required Travis Updates

In the upcoming lecture, we will be adding a script to our .travis.yml file. Due to a change in how the Jest library works with Create React App, we need to make a small modification:

    script:
      - docker run USERNAME/docker-react npm run test -- --coverage
     

instead should be:

    script:
      - docker run -e CI=true USERNAME/docker-react npm run test -- --coverage

Additionally, you may want to set the following property to the top of your .travis.yml file:

    language: generic 

You can read up on the CI=true variable here:

https://facebook.github.io/create-react-app/docs/running-tests#linux-macos-bash

and environment variables in Docker here:

https://docs.docker.com/engine/reference/run/#env-environment-variables