# Pihole-Docker-Swarm
---
## Redundant pihole with docker swarm
---
## Table of Contents

* Introdution
* Goal
* Alternatives
* Requirements
* Setup
* Start the service

## General Information

### Introduction

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

The Goal is also archived by manual running pihole with docker on each node

### Requirements

* installed docker engine on each node
* working docker swarm setup
* optional: Backup of existing pihole-config to import 

### Setup

At first choose two nodes wich should be workers to run pihole and
label each of them:

    docker node update --label-add pihole=true node1.your.net

	docker node update --label-add pihole=true node2.your.net

Create a path for saving the settings on each node:

    sudo mkdir -p /opt/pihole/{etc-dnsmasq.d,etc-pihole}

Hint: You can choose another path and edit the volume setting in the docker-compose file.

Start the service:

    docker stack deploy -c docker-compose.yml pihole

Control if the service is running on both nodes:

    docker service ps pihole_pihole

Open the pihole gui of both nodes in your browser under ip-node1:8000 and ip-node2:8000

Import your backup file or manual configure pihole on both nodes.

#### Network setup 

Set the dns server of your router/dhcp-server to the IP of your nodes, so the clients in 
your network are using the pihole-nodes for dns requests

#### Example network setup for OpenWRT

##### DHCP
If you are using OpenWRT as DHCP-Server, in the router gui move to Network/Interface and choose
edit on your lan interface. Go to the "DHCP Server" Tab --> Advanced setting tap and set your
pihole-node-ips under DHCP-Options like this:

     6,ip-node1,ip-node2

This will advertise your pihole-nodes as dns server to your dhcp-clients

##### Static 
If some of your client using static ip configuration and your router-ip as first dns-server,
you can forward their dns request to your pihole-nodes:

In the router gui move to Network --> DHCP and DNS. On the general tab set "DNS forwardings" to
your pihole-ips 

