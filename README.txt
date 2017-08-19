#I decided to describe in this file not only the docker commands, but all of the shell commands I used.
#To test the app simply run 'docker-compose up' and perform the http requests to the localhost:
#curl -X POST -H "Content-type: application/json" http://localhost:3000/data/into/db -d '[ { "a": 1 }, { "b": 2 }, { "c": 3 } ]'
#curl http://localhost:3000/data/from/db

#the set of actions I did:-------------------------------

#setup my virtual linux machine which I will be running the docker test task on
vagrant init centos/7
vagrant up

#connect to the created machine through ssh
vagrant ssh

#login as root
sudo su

#install nano
yum -y install nano

#install docker
curl https://get.docker.com/ | bash

#install docker-compose
curl -L https://github.com/docker/compose/releases/download/1.15.0/docker-compose-`uname -s`-`uname -m` > /usr/local/bin/docker-compose
chmod +x /usr/local/bin/docker-compose && PATH=$PATH:/usr/local/bin/

#install nodejs
curl --silent --location https://rpm.nodesource.com/setup_8.x | sudo bash
yum -y install nodejs

#install the express generator
npm install -g express-generator

#create the project directory
mkdir /usr/myapp && cd /usr/myapp

#create a template for our test app and install dependencies
express myapp
cd myapp && npm install

# add mongodb driver to package.json
nano package.json #add the line "mongodb": "^2.2.9" to the dependencies section

#modify routes/index.js
nano routes/index.js

# pull the needed docker images
docker pull mhart/alpine-node:latest
docker pull mongo

# create a dockerfile and modify it
touch Dockerfile && nano Dockerfile

#build the docker image
docker build -t myapp .

#test the created image
docker run -p 3000:3000 -ti myapp
curl http://localhost:3000

#upload the docker image to the repository
docker login --username rubysparkle
docker images #get the id
docker tag 3505481d55e0 rubysparkle/myfirstrep:latest
docker push rubysparkle/myapp:latest

#create and modify docker compose file
touch docker-compose.yml
nano docker-compose.yml

#run the compose file
docker-compose up

#test the composed app
curl -X POST -H "Content-type: application/json" http://localhost:3000/data/into/db -d '[ { "a": 1 }, { "b": 2 }, { "c": 3 } ]' #insert to the db
curl http://localhost:3000/data/from/db #get data from the db

#install git
yum install -y git

#upload the build to github
git config --global user.email "chexandro@gmail.com"
git init
git remote add origin https://github.com/ruby-sparkle/MyRep.git
git add .
git commit -m "MyFirstCommit"
git push -u origin master
