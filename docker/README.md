References:
https://github.com/wsargent/docker-cheat-sheet
http://www.cheatography.com/aabs/cheat-sheets/docker-and-friends/
https://www.axian.com/2015/07/16/docker-cheat-sheet/


###Help on a command.
docker COMMAND --help




#==================>>>>                     <<<<==================#
#==================>>>> Registry cheatsheet <<<<==================#
#==================>>>>                     <<<<==================#

#Run the docker registry localy.
docker run -d -p=5000:5000 stackbrew/registry:latest

#Pull the official ubuntu images from the hub.
docker pull ubuntu:latest

#To push the ubuntu image to our registry, we need to tag it.
docker tag 8dbd9e392a96 127.0.0.1:5000/ubuntu:latest
#Where:latest is the tag we give.
#ubuntu:latest has the same IMAGE ID as the one freshly tagged onto our new registry, looks good.

#Push our tagged image to our registry.
docker push 127.0.0.1:5000/ubuntu

#Create a docker file to build FROM the pushed image.
FROM 127.0.0.1:5000/ubuntu:latest
 
# tweak ubuntu's initctl
RUN dpkg-divert --local --rename --add /sbin/initctl
RUN ln -s /bin/true /sbin/initctl
 
RUN apt-get install -y memcached
 
ADD memcached.sh /
EXPOSE 11211
CMD    ["/memcached.sh"]
USER daemon

#Build our custom image from the one in the registry.
docker build -t 127.0.0.1:5000/ubuntu/memcached:latest .

#Push the customly built image to the registry.
docker push 127.0.0.1:5000/ubuntu/memcached

#Run our image.
docker run -e MY_VAR=666 -t -i 127.0.0.1:5000/ubuntu/memcached:latest /bin/bash

#To see a tree output of which containers are running which procesess.
ps axjf

#Now stop your registry and remove all data
docker stop registry && docker rm -v registry

TODO:
#Docker registry TLS connection (certificate)
https://docs.docker.com/registry/insecure/















#==================>>>> 					  <<<<==================#
#==================>>>> Containers cheatsheet <<<<==================#
#==================>>>>                       <<<<==================#

###To execute a command inside a running container.
docker exec -t -i <mycontainer> <myshell>
To enter inside a running container and attach a new shell process use:
docker exec -it foo /bin/bash.

###To attach yourself to a container which already has a TTY running.
docker attach <containerID>

###If you want to map a directory on the host to a docker container.
docker run -v $HOSTDIR:$DOCKERDIR

###Shows containers resource usage statistics.
docker stats

###Copy files or folders between a container and the local filesystem..
docker cp
  
###Turns container filesystem into tarball archive stream to STDOUT.
docker export

###Create a new filesystem image from the contents of a tarball
docker import

#Delete all containers.
docker rm $(docker ps -a -q)

###Run container and remove intermediate images.
docker run --rm ...

### Remove exited containers
docker rm -vf $(docker ps -a -f status=exited -q) 2>/dev/null









#==================>>>> 					  <<<<==================#
#==================>>>> Images cheatsheet     <<<<==================#
#==================>>>>                       <<<<==================#

###Build image from Dockerfile and tag it (give name)
docker build . -t graylog # Named the container graylog

###Remove an image.
docker rmi

###Delete dangling images
docker rmi $(docker images -q -f dangling=true)

###Remove all untaged images (registry cleanup)
docker rmi $(docker images | grep "^<none>" | awk "{print $3}")

###Delete all images
docker rmi $(docker images -q)

###Commit changes made to a local docker container to save any changes made locally.
docker commit container_id image_name

###Load an image from a tar archive.
docker load
	











#==================>>>> 					  <<<<==================#
#==================>>>>         MISC          <<<<==================#
#==================>>>>                       <<<<==================#

###Compy file to a running container.
FULL_CONTAINER_ID = $(docker inspect -f '{{.Id}}' $NSM_CONTAINER_NAME) $ sudo cp file.txt /var/lib/docker/aufs/mnt/$(docker inspect -f '{{.Id}}' $NSM_CONTAINER_NAME)/root/file.txt
