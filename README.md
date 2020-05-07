## ISO Builder

This ISO builder is fork of the good work from the elementary crew.  Many thanks to the devs there https://github.com/elementary/os

## Building Locally

As Ubuntu DDE is built with the Debian version of `live-build`, not the Ubuntu patched version, it's easiest to build an iso in a Debian VM or container. This prevents messing up your host system too.

The following example uses Docker and assumes you have Docker correctly installed and set up:

 1) sudo apt install docker.io

 2) sudo docker pull debian

 3) Customise the docker image to have the ability to build eoan/focal

    sudo docker run --privileged -it debian:latest

    note the containerID in the root prompt i.e. /# means you are now in the container
    
    e.g. root@ea5126f14ac9:/#

    Now install debootstrap package and create a focal (or groovy script)

    apt update && apt install debootstrap git nano -y

    Make sure you check for the latest Ubuntu Release scripts-eoan or focal; currently at disco (run ls)
    
    cd /usr/share/debootstrap/scripts; cp disco (ver your building); cd ;
    Substitute (ver your building) for as of May 7 2020:
    focal -> 20.04 LTS
    groovy -> 20.10
    
    Clone the iso-builder

    mkdir /home/ubuntudde; cd /home/ubuntudde; git clone https://github.com/UbuntuDDE/iso-builder -b ubuntudde; cd iso-builder; 
    
at this point configure etc/terraform.conf for the build you wish to make e.g. 20.04 and focal - ensure you decide between unstable or all PPAs (important step)

When ready: ./build.sh

    This will eventually complete - ignore any errors EXCEPT for 404 repository errors.  If you get those then investigate why and once fixed exit and start the instructions again.  This step will take 20-60 minutes depending on your internet speed and host OS CPU power

exit the container and commit the results

    exit
    
You will now be back on your host O
    
    containerID=$(sudo docker ps -l -q)
    sudo docker commit $containerID ;  sudo docker stop $containerID;sudo docker start -i $containerID
    
    
e.g.   sudo docker commit ea5126f14ac9

Stop the docker container (important step)

 3) Run the build by starting a container:
    
    You will now be back in the container i.e. with a /# prompt

    cd /home/ubuntudde/iso-builder; ./terraform.sh
    
    This will take approx 20-60 minutes but will depend on your host OS CPU power and internet speed

 4) When done, your image will be in the builds folder.

    On your host, copy the build folder from your docker container

    sudo docker cp containerID:/home/ubuntudde/iso-builder/builds/amd64 .

    Finish by shutting down the container

    sudo docker stop containerID

## Docker hints

To see the list of docker containers

    sudo docker ps -a

To remove a docker container using the Container ID listed with the above command

    sudo docker rm containerid
    
To remove a docker image

    sudo docker images
    
    sudo docker rmi imageID

To stop a docker container when running

    sudo docker stop containerid



## Further Information

More information about the concepts behind `live-build` and the technical decisions made to arrive at this set of tools to build an .iso can be found [on the wiki](https://github.com/elementary/os/wiki/Building-iso-Images).
