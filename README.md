## Pihole-Docker-Swarm - Run 2 Pihole instances with docker stack
---
## Table of Contents

* Introdution
* Goal
* Alternatives
* Requirements
* Setup

## General Information

### Introdution

A docker-compose file to run multiple instances of pihole in your docker swarm 

Today we use the internet with countless connections and datatransfers in the background
we don´t need and want. A great solution to keep this things out of your network is the 
dns filter pihole. (Visit the project page )(https://pi-hole.net/) 

I was using pihole in a dockercontainer on a pi3 for a long time. One day the pi died while
i was not at home. The pi was the only dns server in our lan, so the family went offline 
and i was responsible. Singel point of failure

To prevent us from this happening again, we need redundance with a second instance of pihole.

Since a short time some raspi pis in my lan are assembled as a [docker swarm](https://docs.docker.com/engine/swarm/)
and i thougt it would be nice to roll out two instances of pihole with docker stack

### Goal

* Always have 2 instances of pihole running for redundancy
* Promote both instances to the dhcp clients in the lan

### Alternatives

The Goal is rechead too by simply running pihole with docker on earch node

### Requirements

* installed docker engine on each node
* working docker swarm setup

### Setup

At first choose two or more nodes wich should be workers to run pihole and
label each of them:

    docker node update --label-add pihole=true pi1.my.lan

On each node a path for saving settings ist needed. 

    sudo mkdir -p /opt/pihole/{etc-dnsmasq.d,etc-pihole}

You can choose another path and edit the volume setting in the docker-compose file.












