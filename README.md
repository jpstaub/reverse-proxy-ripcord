# reverse-proxy-ripcord:
This repository is meant as a companion repository to [kobo-docker-ripcord](https://github.com/jpstaub/kobo-docker-ripcord).
However, it can be used on its own to set up a reverse-proxy environment for other docker containers. The docker-compose files are structured to provide **WEB** (https) or **LAN** (http) network connectivity to services running in docker containers. `reverse-proxy-ripcord` can be used in the following ways with little in the way of configuration on the part of the end user.

**Note: Command line instructions must be executed inside the reverse-proxy-ripcord directory**

# Prerequisite knowledge base:
[Docker Command Line](https://docs.docker.com/engine/reference/commandline/cli/)

[Docker Compose Command Line](https://docs.docker.com/compose/reference/)

[Server Side Firewall](https://help.ubuntu.com/community/UFW)

[Public DNS Configuration](http://freedns.afraid.org/) -- for **Option 1 below**

[Local DNS Configuration](https://superuser.com/questions/45789/running-dns-locally-for-home-network) -- for **Option 2 below**

[Router Configuration](https://www.dd-wrt.com/wiki/index.php/Tutorials) -- specifically port forwarding

## Option 1 - Reverse Proxy with SSL support:
This repository can be used as a stand alone repository to set up a [jwilder reverse proxy](https://github.com/jwilder/nginx-proxy) server with [companion SSL](https://github.com/JrCs/docker-letsencrypt-nginx-proxy-companion) support. This option would be taken by doing something like the following at the command line:
```
$ ln -s docker-compose.web.yml docker-compose.yml
$ docker-compose up -d
```
The first line above forms a [symlink](https://kb.iu.edu/d/abbe) allowing the user to run [docker-compose commands](https://docs.docker.com/compose/reference/) against **docker-compose.web.yml** as shown in the second line above. If there is a need to remove the symlink do the following:

    $ rm docker-compose.yml

## Option 2 - Reverse Proxy without SSL support:
This repository can be used as a stand alone repository to set up a [jwilder reverse proxy](https://github.com/jwilder/nginx-proxy) server. This option would be taken by doing something like the following at the command line:
```
$ ln -s docker-compose.lan.yml docker-compose.yml
$ docker-compose up -d
```
The first line above forms a [symlink](https://kb.iu.edu/d/abbe) so that the user can use [docker-compose commands](https://docs.docker.com/compose/reference/) against **docker-compose.lan.yml** as shown in the second line above. If there is a need to remove the symlink it would be done with the following:

    $ rm docker-compose.yml

# Usage
1. [Clone](https://help.github.com/articles/cloning-a-repository/) this repository onto a server equipped with Docker Engine and Docker Compose.
2. Decide which option is appropriate for your use case. **Option 1** is meant for **WEB** deployment where there is a need to provide secure https communication between clients and servers with SSL. However, it also maintains the ability to serve http traffic depending on the configuration of containers added to the Docker host. **Option 2** is meant for **LAN** deployment where there is NO need to provide secure https communications between clients and servers with SSL. Both **Option 1** and **Option 2** are configured with a test web server that will display the following image if deployed successfully. 

![test web page](./docs/test_web_page.PNG)

3. Make the appropriate `symlink` according to the option chosen.
4. Make the appropriate entires in `webserver.config`.
5. Make the appropriate entires in your local or public DNS system. See `DNS Records + Router Settings` for guidance on settings.
6. [Forward ports](https://www.dd-wrt.com/wiki/index.php/Tutorials) 80 (http) and 443 (https) if deploying via **Option 1** to allow web access.
7. Disable any server side firewalls for testing.
8. Start an instance with:
    $ docker-compose up -d
9. Watch the logs with:
    $ docker-compose logs -f
10. With clean logs, browse to the test address entered in `webserver.conf`. The test page above proves a successful deployment.     
11. Stop an instance with:
    $ docker-compose stop
12. Remove all containers associated with the instance with:
    $ docker-compose down
    
# No Worky!
An unsuccessful test is usually the result of network configuration problems. 

1. Did you use the Docker host local IP address for local network configuration and your public IP address for public DNS configuration?

2. For **Option 1** deployments: Can you successfully [ping](https://www.lifewire.com/ping-command-2618099) your test web server address that was set in `webserver.conf`? If you got a good ping check your router port forwarding. If the router port forwarding looks good double check that you disabled the firewall on the Docker host. If you did not get a good ping check your public DNS settings to include the IP address. If you have a dynamic IP address your need [forward IP address changes](https://freedns.afraid.org/guide/dd-wrt/) to your DNS provider.

3. For **Option 2** deployments: Can you [ping](https://www.lifewire.com/ping-command-2618099) your local IP address using the test web server address that was set in `webserver.conf`? If not, you may need to [change your DNS server](https://www.lifewire.com/how-to-change-dns-servers-in-windows-7-2626271) your computer is using. This presumes that you have already correctly set up a local DNS server on your network. 

4. And so much more. Networking can be a grinding challenge. Remember the fundamentals and use simple tools like ping to narrow down where the problem lies.
