## Here I share my experience and troubles I faced during when  I Used the following steps to download and run a simple app in a Docker container for the first time:

This was written with the intent to tell everyone about my experience performing the tasks in this exercise. Include any troubleshooting I did, as well as improvements I'd make to the existing procedure to make it more clear. :

First, we have to install Docker and their Respective Supporting Environment for smoothing experience :

- Installed Docker Desktop .
- A Git client.
- An IDE or a text editor to edit files. Docker recommends using Visual Studio Code.
- A conceptual understanding of containers and images.

## Step 1:
Get the app
Before I can run the application, I need to get the application source code onto your machine.

Clone the getting-started repository using the following command:
```bash
git clone https://github.com/docker/getting-started.git
```
View the contents of the cloned repository. Inside the getting-started/app directory, I  see package.json and two subdirectories (src and spec).

In order to build the container image, I need to use a Dockerfile. A Dockerfile is simply a text-based file with no file extension. A Dockerfile contains a script of instructions that Docker uses to create a container image.

In the app directory, the same location as the package.json file, I create a file named Dockerfile. where I can use the following commands below to create a Dockerfile.

Mac 
In the terminal, run the following commands listed below.
 ```bash
 cd getting-started/app
```
Create an empty file named Dockerfile.
 ```bash
 touch Dockerfile
 ```
Using a text editor or code editor, add the following contents to the Dockerfile:

```bash
# syntax=docker/dockerfile:1
FROM node:18-alpine
WORKDIR /app
COPY . .
RUN yarn install --production
CMD ["node", "src/index.js"]
EXPOSE 3000
Note
```
```bash
 docker build -t getting-started .
 ```
The docker build command uses the Dockerfile to build a new container image. I have noticed that Docker downloaded a lot of “layers”. This is because I instructed the builder that i wanted to start from the node:18-alpine image. But, since i didn’t have that in my machine, Docker needed to download the image.

- Start an app container:

Now that I have an image, i can run the application in a container. To do so, i  use the docker run command.
Start the container using the docker run command and specify the name of the image i just created:

```bash
 docker run -dp 3000:3000 getting-started
```
i use the -d flag to run the new container in “detached” mode (in the background). i also use the -p flag to create a mapping between the host’s port 3000 to the container’s port 3000. Without the port mapping, i wouldn’t be able to access the application.

After a few seconds, open your web browser to http://localhost:3000. i can see my app.
![sport Roster](/path/to/image.png "sport Roster")

## Step 2 :

Update the application:

In Step 2, i containerized a Sports Roster application. In this part, I updated the application and container image. i was also learn how to stop and remove a container.

- Update the source code
In the steps below, i was changed the “empty text” when you don’t have any todo list items to “You have no todo items yet! Add one above!”
In the src/static/js/app.js file, update line 56 to use the new empty text.

```bash
...
 -                <p className="text-center">No items yet! Add one above!</p>
 +                <p className="text-center">You have no todo items yet! Add one above!</p>
 ...
 ```

Build my updated version of the image, using the same docker build command .


 ```bash
 docker build -t getting-started .
```
Start a new container using the updated code.


```bash
docker run -dp 3000:3000 getting-started
```
I  saw an error like this (the IDs will be different):


The error occurred because i was not able to start the new container while your old container is still running. The reason is that the old container is already using the host’s port 3000 and only one process on the machine (containers included) can listen to a specific port. To fix this, you need to remove the old container.

- Remove the old container
To remove a container, i first need to stop it. Once it has stopped, i can remove it. i can remove the old container using the CLI or Docker Desktop’s graphical interface. Choose the option that you’re most comfortable with.

- Start the updated app container

Now, start your updated app using the docker run command.

```bash
 docker run -dp 3000:3000 getting-started
```
Refresh your browser on http://localhost:3000 and you should see your updated help text.


##Share the application:

Now that I’ve built an image, let’s share it! To share Docker images, I've to use a Docker registry. The default registry is Docker Hub and is where all of the images we’ve used have come from.
- Create a repo

 To push an image, we first need to create a repository on Docker Hub.  For the repo name, use getting-started. Make sure the Visibility is Public.
  Click the Create button!
 If i look at the image below an example Docker command can be seen. This command will push to this repo.Push the image
In the command line, try running the push command you see on Docker Hub. Note that your command will be using your namespace, not “docker”.

```bash
 $ docker push docker/getting-started
 The push refers to repository [docker.io/imimperfectionsoul/getting-started]
 An image does not exist locally with the tag: docker/getting-started
Why did it fail? The push command was looking for an image named docker/getting-started, but didn’t find one. If you run docker image ls, you won’t see one either.
```

To fix this, i need to “tag” my existing image I had built to give it another name.
Then i Login to the Docker Hub using the command ```bash docker login -u imimperfectionsoul. ```

Use the docker tag command to give the getting-started image a new name. Be sure to swap out YOUR-USER-NAME with your Docker ID.

```bash
 docker tag getting-started imimperfectionsoul/getting-started
 ```

then i try my push command again.  i'm copy the value from Docker Hub, as i didn’t add a tag to the image name. If i didn’t specify a tag, Docker will use a tag called latest.


 ```bash
 docker push imimperfectionsoul/getting-started
```
Run the image on a new instance
Now that my image has been built and pushed into a registry, then i  try running my  app on a brand new instance that has never seen this container image! To do this, i used Play with Docker.

Once I logged in, click on the ADD NEW INSTANCE option on the left side bar. After a few seconds, a terminal window opens in your browser.
In the terminal, I start freshly pushed app.

 ```bash
 docker run -dp 3000:3000 YOUR-USER-NAME/getting-started
```
- In the starting i faced trouble to pulled down the image because due to my operating system the following error are shown :

```bash
WARNING: The requested image's platform (linux/arm64/v8) does not match the detected host platform (linux/amd64) and no specific platform was requested
0f23831b83b5d3580a60450e334396e67b42294b1526d6eeadbcc97f22dfa127
```
but i over come from it by adding some requirement in the docker which help to match the detected host platform.
After i see  the image get pulled down and eventually start up!

## The container’s filesystem :

 In the container file system  When a container runs, it uses the various layers from an image for its filesystem. Each container also gets its own “scratch space” to create/update/remove files. Any changes won’t be seen in another container, even if they are using the same image. 
 - Persist the todo data
 it’s simply a relational database in which all of the data is stored in a single file. While this isn’t the best for large-scale applications, it works for small demos.
 
 To create todo.db i used terminal command 
 
 Create a volume by using the docker volume create command.

```bash
 docker volume create todo-db
 ```
Stop and remove the todo app container once again in the Dashboard ```bash (or with docker rm -f <id>)``` as it is still running without using the persistent volume.

Start the todo app container, but add the --mount option to specify a volume mount. We will give the volume a name, and mount it to /etc/todos in the container, which will capture all files created at the path.


 ```bash 
 docker run -dp 3000:3000 --mount type=volume,src=todo-db,target=/etc/todos getting-started
```
Once the container starts up, open the app and add a few items to your todo list
Stop and remove the container for the todo app. Use the Dashboard or docker ps to get the ID and then  ```bash docker rm -f <id>  ``` to remove it.

Start a new container using the same command from above.

Open the app. i see my items still in the list!

Go ahead and remove the container when i was done checking out my list.

- Untill three step I Found liitle bit difficulty to manage the container and frequently delted the old one when created the new one unless its shows error.

## Use bind mounts :
 
A bind mount is another type of mount, which lets you share a directory from the host’s filesystem into the container. When working on an application, you can use a bind mount to mount source code into the container. The container sees the changes you make to the code immediately, as soon as you save a file. This means that you can run processes in the container that watch for filesystem changes and respond to them

In other words, when I change the source code into the container that changes is immediately shown in My App When I Just refresh it.
The following steps i followed  how to run a development container with a bind mount that does the following:

- Mount our source code into the container
- Install all dependencies
- Start nodemon to watch for filesystem changes
Make sure you don’t have any getting-started containers currently running
 ```bash
docker run -dp 3000:3000 \
    -w /app --mount type=bind,src="$(pwd)",target=/app \
    node:18-alpine \
    sh -c "yarn install && yarn run dev"
 ```
- e.g
 Now, make a change to the app. In the src/static/js/app.js file, on line 109, change the “Add Item” button to simply say “Add”:
 
 ```bash
  -{submitting ? 'Adding...' : 'Add Item'}
  +{submitting ? 'Adding...' : 'Add'}
 ```
 
Refresh the page in the web browser, and i can see the change reflected almost immediately. It might take a few seconds for the Node server to restart. 

## Container networking :
Here i Connected the Container to Network / The Mysql Server by following step 

Create the network.

 ```bash
 docker network create todo-app
  ```
Start a MySQL container and attach it to the network. We’re also going to define a few environment variables that the database will use to initialize the database (see the “Environment Variables” section in the MySQL Docker Hub listing).

 ```bash
 docker run -d \
     --network todo-app --network-alias mysql \
     -v todo-mysql-data:/var/lib/mysql \
     -e MYSQL_ROOT_PASSWORD=secret \
     -e MYSQL_DATABASE=todos \
     mysql:8.0
 ```
 To confirm we have the database up and running, connect to the database and verify it connects.
 ```bash
 docker exec -it <mysql-container-id> mysql -u root -p
```
- After Connecting with the Network & Run my app with MySQL
 but I faced some issues because in these existing procedures are not clear after some research from the online guide and portal I found the error and correct and as connect the container to our app network
 
 ```bash
 docker run -dp 3000:3000 \
   -w /app -v "$(pwd):/app" \
   --network todo-app \
   -e MYSQL_HOST=mysql \
   -e MYSQL_USER=root \
   -e MYSQL_PASSWORD=secret \
   -e MYSQL_DB=todos \
   node:18-alpine \
   sh -c "yarn install && yarn run dev"
```
then i Open the app in the browser and add a few items to your app list.
Connect to the mysql database and prove that the items are being written to the database.

 ```bash
 docker exec -it <mysql-container-id> mysql -p todos
  ```
  nd in the mysql shell, run the following query to check the databases;

```bash
 select * from todo_items;
 ```
 - the most challenging  part of these assesment is connectincting the conatiner to the server because many time file not found the location in the database of app and gave error  .
 ## Use Docker Compose;
 
 docker Compose is a tool that was developed to help define and share multi-container applications. With Compose, we can create a YAML file to define the services and with a single command, can spin everything up or tear it all down. in these just follow the existing procedure and completed where I define the app service by using some command.
 
 ## Image-building best practices;
 In these have built an image, it is a good practice to scan it for security vulnerabilities using the docker scan command. Docker has partnered with Snyk to provide the vulnerability scanning service.
 
 ## Image layering and Layer caching :
  The docker image history command to see the layers in the getting-started image I created earlier in the App.
  Going back to the image history output, I saw that each command in the Dockerfile becomes a new layer in the image. 
  To fix this, I had to restructure our Dockerfile to help support the caching of the dependencies. For Node-based applications, those dependencies are defined in the package.json file.
  
  ## EXPERIENCE 
  - The overall experience of this assessment is amazing where I encountered so many challenges  I faced the first time while setting up the Docker and running the app with some repeating errors after code evaluation and guidelines I was able to resolve them. I found myself in a great position after when I connect the docker to the network or server before that I found it difficult to run the app along with SQL because docker is unable to find a file when I run the SQL command. After that, I go through the guidelines and was able to complete all the remaining tasks to run an Application in Docker.
  - for troubleshooting the problem I advise all to read and follow the guidance and for Prerequisites Read something about your system and Operating system . because it reduces some fatal error that is caused by it. and for my experience, I found many of the steps are understandable and clear but you can find more about some open source and other sources to get more ways to sort out your problem or error you faced. like I faced a problem connecting with the database so I re-evaluate the code and read the guideline some open sources that are useful over the guidelines helped me a lot to Docker containers be used to make deploying and maintaining applications easier.
