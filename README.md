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

## General Information

### Introdution

A docker-compose file to run multiple instances of pihole in your docker swarm 

Today we use the internet with countless connections, datatransfers and trackings in the
background we don´t need and want. A great solution to keep this things out of your
network is the dns filter pihole. (Visit the project page )(https://pi-hole.net/) 

I was using pihole in a dockercontainer on a pi3 for a long time. One day the pi died while
i was not at home. The pi was the only dns server in our lan, so the family went offline 
and i was responsible. Singel point of failure

To prevent us from this happening again, we need redundance with a second instance of pihole.

Since a short time some raspi pis in my lan are assembled as a [docker swarm](https://docs.docker.com/engine/swarm/)
and i thought it would be nice to roll out two instances of pihole with docker stack

### Goal

* Always have 2 instances of pihole running for redundancy
* Promote both instances to the dhcp clients in the lan

### Alternatives

The goal is also archived by manual running pihole with docker on each node

### Requirements

* working docker swarm setup
* port 53 not in use on the nodes that should run pihole 
* optional: Backup of existing pihole-config to import 

### Setup

At first choose two nodes wich should be workers to run pihole and
label each of them:

    docker node update --label-add pihole=true node1.your.net

	docker node update --label-add pihole=true node2.your.net

2.

Deploy 2 instances of pihole on your nodes:

	docker stack deploy -c docker-compose.yml pihole

It could take some time until the pihole dockerimage is loaded and started.
Check the state of the service with:

	docker service ps pihole_pihole 

3.

Open the pihole gui in your browser: http://IP-of-your-node:8000/admin/ and 
configure pihole to your needs. (e.g. add blocklists) 

Test the function of this node with a client by using the node-ip as dns-server-ip

4. 

Export your settings on the node with the export function under Settings/Teleporter
and import it at the same place on your second node.

5. 

Add both node-ips as dns-servers to your clients or add them as dhcp-option to your
dhcp-server (e.g. 6,192.168.0.10,192.168.0.20) 

#### OpenWRT - Settings to use the pihole nodes as dns server

##### DHCP
Open the router gui and move to Network/Interface, klick the edit button of your lan interface.
Switch to the "DHCP Server" --> Advanced setting tap and set your
pihole-node-ips under DHCP-Options like this:

     6,ip-node1,ip-node2

This will advertise your pihole-nodes as dns server to your dhcp-clients

