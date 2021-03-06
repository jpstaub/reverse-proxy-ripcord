# reverse-proxy-ripcord:
This repository is meant as a companion repository to [kobo-docker-ripcord](https://github.com/jpstaub/kobo-docker-ripcord).
However, it can be used on its own to set up a reverse-proxy environment for other docker containers. The docker-compose files are structured to provide **WEB** (https) or **LAN** (http) network connectivity to services running in docker containers. `reverse-proxy-ripcord` can be used in the following ways with little in the way of configuration on the part of the end user.

**Note: Command line instructions must be executed inside the `reverse-proxy-ripcord` directory.**

# Prerequisite knowledge base:
[Docker Command Line](https://docs.docker.com/engine/reference/commandline/cli/)

[Docker Compose Command Line](https://docs.docker.com/compose/reference/)

[Server Side Firewall](https://help.ubuntu.com/community/UFW)

[Public DNS Configuration](http://freedns.afraid.org/) -- for **Option 1 below**

[Local DNS Configuration](https://superuser.com/questions/45789/running-dns-locally-for-home-network) -- for **Option 2 below**

[Router Configuration](https://www.dd-wrt.com/wiki/index.php/Tutorials) -- specifically port forwarding

[ping](https://www.lifewire.com/ping-command-2618099) -- a method to test network connectivity 

[nslookup](https://www.lifewire.com/what-is-nslookup-817516) -- a method to test DNS resolution

[ipconfig](https://www.lifewire.com/what-is-nslookup-817516) -- a method to see the ipconfig of a given computer

[DNS settings Windows](https://www.windowscentral.com/how-change-your-pcs-dns-settings-windows-10)

[DNS settings Mac](http://osxdaily.com/2015/12/05/change-dns-server-settings-mac-os-x/)

Running the following command will give linux manual page specifics about a command that is giving you trouble:
```
$ man [command]
```
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

4. Make the appropriate entires in [`webserver.config`](./webserver.config.txt).

5. Make the appropriate entires in your public or local DNS system. See [`DNS Records + Router Settings`](./docs/DNS_Records_+_Router_Settings.txt") for guidance on settings. **Option 1** requires public DNS configuration. **Option 2** requires local DNS configuration.

An example of an **Option 1** public DNS setup from [FreeDNS.afraid.org](http://freedns.afraid.org/) is shown below.

![dns records](./docs/dns_records.PNG)

6. `ping` the test address that was set in [`webserver.config`](./webserver.config.txt). Adjust DNS settings until a successful `ping` response is returned. See below for what a successful ping from a command line looks like.

![good ping](./docs/good_ping_check.PNG)

7. **Option 1 Only:** [Forward ports](https://www.dd-wrt.com/wiki/index.php/Tutorials) 80 (http) and 443 (https). Ports should be forwarded to the **IP address** of the **Docker host** on which the **instance of `reverse-proxy-ripcord` is deployed**. See [`DNS Records + Router Settings`](./docs/DNS_Records_+_Router_Settings.txt") for guidance on settings.

An example [port forwarding](https://www.dd-wrt.com/wiki/index.php/Port_Forwarding) setup from an ASUS router is shown below.

![router config](./docs/router_config.PNG)

8. Disable any server side [firewalls](https://help.ubuntu.com/community/UFW) for testing.

9. Start an instance with:

    ```$ docker-compose up -d```
    
10. Watch the logs with:

    ```$ docker-compose logs -f```
    
11. With clean logs, browse to the test address entered in `webserver.conf`. The test page included above proves a successful deployment.    

12. Stop an instance with:

    ```$ docker-compose stop```
    
13. Remove all containers associated with the instance with:

    ```$ docker-compose down```
    
# No Worky!
An unsuccessful test is usually the result of network configuration problems. 

1. Did you use the **Docker host local IP address** for **local network configuration** and your **public IP address** for **public DNS configuration**?

2. For **Option 1** deployments: Can you successfully [ping](https://www.lifewire.com/ping-command-2618099) your test web server address that was set in `webserver.conf`? A representation of a good `ping` check is included above. A represenation of a bad `ping` check is included below.

![bad ping](./docs/bad_ping_check.PNG)

If you got a good ping check your router port forwarding. An example of the logs output for bad port forwarding is included below.

![bad router configuration](./docs/bad_port_fwd.PNG)

If the router port forwarding looks good double check that you disabled the firewall on the Docker host. If you did not get a good ping check your public DNS settings to include the IP address. If you have a dynamic IP address your need to [forward IP address changes](https://freedns.afraid.org/guide/dd-wrt/) to your DNS provider to ensure long term connectivity.

3. For **Option 2** deployments: Can you [ping](https://www.lifewire.com/ping-command-2618099) the local test web server address set in `webserver.conf`? If not, you may need to [change the DNS server](https://www.lifewire.com/how-to-change-dns-servers-in-windows-7-2626271) your computer is using. This presumes that you have already correctly set up a local DNS server on your network. 

4. And so much more. Networking can be a grinding challenge. Remember the fundamentals and use simple tools like ping to narrow down where the problem lies.
