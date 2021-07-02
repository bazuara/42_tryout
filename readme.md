# How to start learning javascript and node during a 42 level test...

### ...or the story of what did I achieve during 4 hours of a test with a stack I don't know anything about

## Step 0
Freak out. Really, this is some stuff you don't know anyting about. You are doomed etc... Now, that's out of your system, so let's really begin with it.

## Step 0.1
Analize, what do they ask you to do? Ok, its a web app, that consumes data form the 42 API. You know how to use it, so thats not a problem. If it's a problem, first read this and create your first API app [here](https://api.intra.42.fr/apidoc/guides/getting_started).

Next, on to the required stack... HTML, ok... CSS, not my favourite but I can manage... NodeJS... what is that? Oh, a JavaScript framework? Ok, I don't know anything about JavaScript, so I'll spend the next 10 to 20 minutes back at step 0. BRB.

## Step 1
Now, you've overcome the anxiety of not knowing that stack, so lets start with what you know, and how can it be put to good use. First you need somewhere to develop your "app", some kind of playground. You can use brew and install npm, the node package manager to star playing locally, but you don't know how to undo half of the stuff you are going to type, so nor recomended. Besides, I'm runing low on space at my user folder, so it's definetly a no-no. 

**Docker**, my loyal friend, here we are again, at the face of adversity... Join me so we can do something to avoid an empty repo in this battle!

Let's start simple and build a small image with node installed. 
Create a new file called ```Dockerfile```. It should look like this:

```
FROM node:14
RUN npm install
CMD echo "no CMD at the moment, we are going to use this text as a placeholder until we figure things out"
```
it should buy a docker image with ```docker build -t node_test .```
Let's start playing with it by runing ```docker run -it --rm node_test bash``` Note the --rm flag so the container gets deleted when it's done, so we dont have to cleanup after every test run we do, but also remember that everyting you do inside that container is volatile, so it's going to be destroyed when it stops. It should open a shell inside that container, so lets check whats inside... A single **app folder**. Here it's where all the code I don't know should go... ok, it's a good start, at least I know where I can put my stuff, so lets redo the image so it can be saved outside.

```
FROM node:14
RUN npm install
WORKDIR /app # This is the magic folder we found out before
CMD echo "no CMD at the moment, we are going to use this text as a placeholder until we figure things out"
```
We should run again a ```docker build -t node_test .``` Everytime we touch the ```Dockerfile``` but this time in our run is going to be a folder mounted, so the changes inside the container can exist outside the container. We want to make our local folder link to the app folder inside, so let's try to run it with ```docker run -it -v ./:/app --rm node_test bash```. It explodes, and doesn't run, you want to know why? Docker doesn't like that kind of filenames with dots and symbols, so it requires an absolute path to work like ```/Users/bazuara/Documents/42_tryout``` but that would absolutely nuke the portability of our  development boilerplate for our "app" so we need a way to fix it. 

Enter a new ally, and old friend, **the shell**! It comes with great tools and gadgets like the environment variables, that are going to help us with this problem. We just simply use $PWD as an argument. Come on, try to type ```echo $CMD``` in your shell, you'll see it returns the current working directory as a string, so we can use that path tu run our container with ```docker run -it -v $PWD:/app --rm node_test bash```

Ok, we've been here for an our and a half, but we finally have some setup where we can start to figure out things. Oh wait, I forgot about **ports**... Ok, let's check that part. It seems that node likes to use ```port 3000``` to publish your "app" so let's ensure that you can reach that port and save hours of screaming at your screen.
> why doesn't shows anything on http://localhost:3000 !? What do you mean you can't connect to the server!?

So, we can run our image with ```docker run -it -v $PWD:/app -p 3000:3000 --rm node_test bash``` so we map the 3000 port inside the container to the 3000 port outside, meaning your localhost.

So, until now this was an all-theorical "let's fix stuff before it crash" guide, but we dont have anything to test that it works, so lets find some easy nodejs project to deploy as our "app" to test if we are on the right path.

Lets begin...

