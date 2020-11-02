
class: title

# Networking and Volumes

![A dense graph network](images/title-container-networking-basics.jpg)

---

## Objectives

We will now run network services (accepting requests) in containers.

At the end of this section, you will be able to:

* Run a network service in a container.

* Manipulate container networking basics.

* Find a container's IP address.

We will also explain the different network models used by Docker.

---

## A simple, static web server

Run the Docker Hub image `nginx`, which contains a basic web server:

```bash
$ docker run -d -P nginx
66b1ce719198711292c8f34f84a7b68c3876cf9f67015e752b94e189d35a204e
```

* Docker will download the image from the Docker Hub.

* `-d` tells Docker to run the image in the background.

* `-P` tells Docker to make this service reachable from other computers.
  <br/>(`-P` is the short version of `--publish-all`.)

But, how do we connect to our web server now?

---

## Finding our web server port

We will use `docker ps`:

```bash
$ docker ps
CONTAINER ID  IMAGE  ...  PORTS                  ...
e40ffb406c9e  nginx  ...  0.0.0.0:32768->80/tcp  ...
```


* The web server is running on port 80 inside the container.

* This port is mapped to port 32768 on our Docker host.

We will explain the whys and hows of this port mapping.

But first, let's make sure that everything works properly.

---

## Connecting to our web server (GUI)

Point your browser to the IP address of your Docker host, on the port
shown by `docker ps` for container port 80.

![Screenshot](images/welcome-to-nginx.png)

---

## Connecting to our web server (CLI)

You can also use `curl` directly from the Docker host.

Make sure to use the right port number if it is different
from the example below:

```bash
$ curl localhost:32768
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
...
```

---

## How does Docker know which port to map?

* There is metadata in the image telling "this image has something on port 80".

* We can see that metadata with `docker inspect`:

```bash
$ docker inspect --format '{{.Config.ExposedPorts}}' nginx
map[80/tcp:{}]
```

* This metadata was set in the Dockerfile, with the `EXPOSE` keyword.

* We can see that with `docker history`:

```bash
$ docker history nginx
IMAGE               CREATED             CREATED BY
7f70b30f2cc6        11 days ago         /bin/sh -c #(nop)  CMD ["nginx" "-g" "…
<missing>           11 days ago         /bin/sh -c #(nop)  STOPSIGNAL [SIGTERM]
<missing>           11 days ago         /bin/sh -c #(nop)  EXPOSE 80/tcp
```

---

## Why are we mapping ports?

* We are out of IPv4 addresses.

* Containers cannot have public IPv4 addresses.

* They have private addresses.

* Services have to be exposed port by port.

* Ports have to be mapped to avoid conflicts.

---

## Finding the web server port in a script

Parsing the output of `docker ps` would be painful.

There is a command to help us:

```bash
$ docker port <containerID> 80
32768
```

---

## Manual allocation of port numbers

If you want to set port numbers yourself, no problem:

```bash
$ docker run -d -p 80:80 nginx
$ docker run -d -p 8000:80 nginx
$ docker run -d -p 8080:80 -p 8888:80 nginx
```

* We are running three NGINX web servers.
* The first one is exposed on port 80.
* The second one is exposed on port 8000.
* The third one is exposed on ports 8080 and 8888.

Note: the convention is `port-on-host:port-on-container`.

---

## Finding the container's IP address

We can use the `docker inspect` command to find the IP address of the
container.

```bash
$ docker inspect --format '{{ .NetworkSettings.IPAddress }}' <yourContainerID>
172.17.0.3
```

* `docker inspect` is an advanced command, that can retrieve a ton
  of information about our containers.

* Here, we provide it with a format string to extract exactly the
  private IP address of the container.

---

## Pinging our container

We can test connectivity to the container using the IP address we've
just discovered. Let's see this now by using the `ping` tool.

```bash
$ ping <ipAddress>
64 bytes from <ipAddress>: icmp_req=1 ttl=64 time=0.085 ms
64 bytes from <ipAddress>: icmp_req=2 ttl=64 time=0.085 ms
64 bytes from <ipAddress>: icmp_req=3 ttl=64 time=0.085 ms
```

---

## Section summary

We've learned how to:

* Expose a network port.

* Manipulate container networking basics.

* Find a container's IP address.

In the next chapter, we will see how to connect
containers together without exposing their ports.

---

## Container network drivers

The Docker Engine supports many different network drivers.

The built-in drivers include:

* `bridge` (default)

* `none`

* `host`

* `container`

The driver is selected with `docker run --net ...`.

---

## The default bridge

* By default, the container gets a virtual `eth0` interface.
  <br/>(In addition to its own private `lo` loopback interface.)

* That interface is provided by a `veth` pair.

* It is connected to the Docker bridge.
  <br/>(Named `docker0` by default; configurable with `--bridge`.)

* Addresses are allocated on a private, internal subnet.
  <br/>(Docker uses 172.17.0.0/16 by default; configurable with `--bip`.)

---

## The null driver

* Container is started with `docker run --net none ...`

* It only gets the `lo` loopback interface. No `eth0`.

* It can't send or receive network traffic.

* Useful for isolated/untrusted workloads.

---

## The host driver

* Container is started with `docker run --net host ...`

* It sees (and can access) the network interfaces of the host.

* It can bind any address, any port (for ill and for good).

* Network traffic doesn't have to go through NAT, bridge, or veth.

* Performance = native!

Use cases:

* Performance sensitive applications (VOIP, gaming, streaming...)

* Peer discovery (e.g. Erlang port mapper, Raft, Serf...)

---

## The container driver

* Container is started with `docker run --net container:id ...`

* It re-uses the network stack of another container.

* It shares with this other container the same interfaces, IP address(es), routes, iptables rules, etc.

* Those containers can communicate over their `lo` interface.
  <br/>(i.e. one can bind to 127.0.0.1 and the others can connect to it.)

---

class: pic

## Single container in a Docker network

![bridge0](images/bridge1.png)

---

class: pic

## Two containers on a single Docker network

![bridge2](images/bridge2.png)

---

class: pic

## Two containers on two Docker networks

![bridge3](images/bridge3.png)

---

## Creating a network

Let's create a network called `dev`.

```bash
$ docker network create dev
4c1ff84d6d3f1733d3e233ee039cac276f425a9d5228a4355d54878293a889ba
```

The network is now visible with the `network ls` command:

```bash
$ docker network ls
NETWORK ID          NAME                DRIVER
6bde79dfcf70        bridge              bridge
8d9c78725538        none                null
eb0eeab782f4        host                host
4c1ff84d6d3f        dev                 bridge
```

---

## Placing containers on a network

We will create a *named* container on this network.

It will be reachable with its name, `es`.

```bash
$ docker run -d --name es --net dev elasticsearch:2
8abb80e229ce8926c7223beb69699f5f34d6f1d438bfc5682db893e798046863
```

---

## Communication between containers

Now, create another container on this network.

.small[
```bash
$ docker run -ti --net dev alpine sh
root@0ecccdfa45ef:/#
```
]

From this new container, we can resolve and ping the other one, using its assigned name:

.small[
```bash
/ # ping es
PING es (172.18.0.2) 56(84) bytes of data.
64 bytes from es.dev (172.18.0.2): icmp_seq=1 ttl=64 time=0.221 ms
64 bytes from es.dev (172.18.0.2): icmp_seq=2 ttl=64 time=0.114 ms
64 bytes from es.dev (172.18.0.2): icmp_seq=3 ttl=64 time=0.114 ms
^C
--- es ping statistics ---
3 packets transmitted, 3 received, 0% packet loss, time 2000ms
rtt min/avg/max/mdev = 0.114/0.149/0.221/0.052 ms
root@0ecccdfa45ef:/#
```
]

---

class: extra-details

## Resolving container addresses

In Docker Engine 1.9, name resolution is implemented with `/etc/hosts`, and
updating it each time containers are added/removed.

.small[
```bash
[root@0ecccdfa45ef /]# cat /etc/hosts
172.18.0.3  0ecccdfa45ef
127.0.0.1       localhost
::1     localhost ip6-localhost ip6-loopback
fe00::0 ip6-localnet
ff00::0 ip6-mcastprefix
ff02::1 ip6-allnodes
ff02::2 ip6-allrouters
172.18.0.2      es
172.18.0.2      es.dev
```
]

In Docker Engine 1.10, this has been replaced by a dynamic resolver.

(This avoids race conditions when updating `/etc/hosts`.)

---

## Service discovery with containers

* Let's try to run an application that requires two containers.

* The first container is a web server.

* The other one is a redis data store.

* We will place them both on the `dev` network created before.

---

## Running the web server

* The application is provided by the container image `ahmedgabercod/trainingwheels`.

* We don't know much about it so we will try to run it and see what happens!

Start the container, exposing all its ports:

```bash
$ docker run --net dev -d -P ahmedgabercod/trainingwheels
```

Check the port that has been allocated to it:

```bash
$ docker ps -l
```

---

## Test the web server

* If we connect to the application now, we will see an error page:

![Trainingwheels error](images/trainingwheels-error.png)

* This is because the Redis service is not running.
* This container tries to resolve the name `redis`.

Note: we're not using a FQDN or an IP address here; just `redis`.

---

## Start the data store

* We need to start a Redis container.

* That container must be on the same network as the web server.

* It must have the right name (`redis`) so the application can find it.

Start the container:

```bash
$ docker run --net dev --name redis -d redis
```

---

## Test the web server again

* If we connect to the application now, we should see that the app is working correctly:

![Trainingwheels OK](images/trainingwheels-ok.png)

* When the app tries to resolve `redis`, instead of getting a DNS error, it gets the IP address of our Redis container.

---

class: extra-details 

## A few words on *scope*

* What if we want to run multiple copies of our application?

* Since names are unique, there can be only one container named `redis` at a time.

* However, we can specify the network name of our container with `--net-alias`.

* `--net-alias` is scoped per network, and independent from the container name.

---

class: extra-details

## Using a network alias instead of a name

Let's remove the `redis` container:

```bash
$ docker rm -f redis
```

* `-f`: Force the removal of a running container (uses SIGKILL)

And create one that doesn't block the `redis` name:

```bash
$ docker run --net dev --net-alias redis -d redis
```

Check that the app still works (but the counter is back to 1,
since we wiped out the old Redis container).

---

class: extra-details

## Names are *local* to each network

Let's try to ping our `es` container from another container, when that other container is *not* on the `dev` network.

```bash
$ docker run --rm alpine ping es
ping: bad address 'es'
```

Names can be resolved only when containers are on the same network.

Containers can contact each other only when they are on the same network (you can try to ping using the IP address to verify).

---

class: extra-details

## Network aliases

We would like to have another network, `prod`, with its own `es` container. But there can be only one container named `es`!

We will use *network aliases*.

A container can have multiple network aliases.

Network aliases are *local* to a given network (only exist in this network).

Multiple containers can have the same network alias (even on the same network). In Docker Engine 1.11, resolving a network alias yields the IP addresses of all containers holding this alias.

---

class: extra-details

## Creating containers on another network

Create the `prod` network.

```bash
$ docker network create prod
5a41562fecf2d8f115bedc16865f7336232a04268bdf2bd816aecca01b68d50c
```

We can now create multiple containers with the `es` alias on the new `prod` network.

```bash
$ docker run -d --name prod-es-1 --net-alias es --net prod elasticsearch:2
38079d21caf0c5533a391700d9e9e920724e89200083df73211081c8a356d771
$ docker run -d --name prod-es-2 --net-alias es --net prod elasticsearch:2
1820087a9c600f43159688050dcc164c298183e1d2e62d5694fd46b10ac3bc3d
```

---

class: extra-details

## Resolving network aliases

Let's try DNS resolution first, using the `nslookup` tool that ships with the `alpine` image.

```bash
$ docker run --net prod --rm alpine nslookup es
Name:      es
Address 1: 172.23.0.3 prod-es-2.prod
Address 2: 172.23.0.2 prod-es-1.prod
```

(You can ignore the `can't resolve '(null)'` errors.)

---

class: extra-details

## Connecting to aliased containers

Each ElasticSearch instance has a name (generated when it is started). This name can be seen when we issue a simple HTTP request on the ElasticSearch API endpoint.

Try the following command a few times:

.small[
```bash
$ docker run --rm --net dev centos curl -s es:9200
{
  "name" : "Tarot",
...
}
```
]

Then try it a few times by replacing `--net dev` with `--net prod`:

.small[
```bash
$ docker run --rm --net prod centos curl -s es:9200
{
  "name" : "The Symbiote",
...
}
```
]

---

class: extra-details


## Good to know ...

* Docker will not create network names and aliases on the default `bridge` network.

* Therefore, if you want to use those features, you have to create a custom network first.

* Network aliases are *not* unique on a given network.

* i.e., multiple containers can have the same alias on the same network.

* In that scenario, the Docker DNS server will return multiple records.
  <br/>
  (i.e. you will get DNS round robin out of the box.)

* Enabling *Swarm Mode* gives access to clustering and load balancing with IPVS.

* Creation of networks and network aliases is generally automated with tools like Compose.

---

class: extra-details

## A few words about round robin DNS

Don't rely exclusively on round robin DNS to achieve load balancing.

Many factors can affect DNS resolution, and you might see:

- all traffic going to a single instance;
- traffic being split (unevenly) between some instances;
- different behavior depending on your application language;
- different behavior depending on your base distro;
- different behavior depending on other factors (sic).

It's OK to use DNS to discover available endpoints, but remember that you have to re-resolve every now and then to discover new endpoints.

---

class: extra-details

## Custom networks

When creating a network, extra options can be provided.

* `--internal` disables outbound traffic (the network won't have a default gateway).

* `--gateway` indicates which address to use for the gateway (when outbound traffic is allowed).

* `--subnet` (in CIDR notation) indicates the subnet to use.

* `--ip-range` (in CIDR notation) indicates the subnet to allocate from.

* `--aux-address` allows specifying a list of reserved addresses (which won't be allocated to containers).

---

class: extra-details

## Setting containers' IP address

* It is possible to set a container's address with `--ip`.
* The IP address has to be within the subnet used for the container.

A full example would look like this.

```bash
$ docker network create --subnet 10.66.0.0/16 pubnet
42fb16ec412383db6289a3e39c3c0224f395d7f85bcb1859b279e7a564d4e135
$ docker run --net pubnet --ip 10.66.66.66 -d nginx
b2887adeb5578a01fd9c55c435cad56bbbe802350711d2743691f95743680b09
```

*Note: don't hard code container IP addresses in your code!*

*I repeat: don't hard code container IP addresses in your code!*

---

## Overlay networks

* The features we've seen so far only work when all containers are on a single host.

* If containers span multiple hosts, we need an *overlay* network to connect them together.

* Docker ships with a default network plugin, `overlay`, implementing an overlay network leveraging
  VXLAN, *enabled with Swarm Mode*.

* Other plugins (Weave, Calico...) can provide overlay networks as well.

* Once you have an overlay network, *all the features that we've used in this chapter work identically
  across multiple hosts.*

---

class: extra-details

## Multi-host networking (overlay)

Out of the scope for this intro-level workshop!

Very short instructions:

- enable Swarm Mode (`docker swarm init` then `docker swarm join` on other nodes)
- `docker network create mynet --driver overlay`
- `docker service create --network mynet myimage`

If you want to learn more about Swarm mode, you can check
[this video](https://www.youtube.com/watch?v=EuzoEaE6Cqs)
or [these slides](https://container.training/swarm-selfpaced.yml.html).

---

class: extra-details

## Multi-host networking (plugins)

Out of the scope for this intro-level workshop!

General idea:

- install the plugin (they often ship within containers)

- run the plugin (if it's in a container, it will often require extra parameters; don't just `docker run` it blindly!)

- some plugins require configuration or activation (creating a special file that tells Docker "use the plugin whose control socket is at the following location")

- you can then `docker network create --driver pluginname`

---

## Connecting and disconnecting dynamically

* So far, we have specified which network to use when starting the container.

* The Docker Engine also allows connecting and disconnecting while the container is running.

* This feature is exposed through the Docker API, and through two Docker CLI commands:

  * `docker network connect <network> <container>`

  * `docker network disconnect <network> <container>`

---

## Dynamically connecting to a network

* We have a container named `es` connected to a network named `dev`.

* Let's start a simple alpine container on the default network:

  ```bash
  $ docker run -ti alpine sh
  / #
  ```

* In this container, try to ping the `es` container:

  ```bash
  / # ping es
  ping: bad address 'es'
  ```

  This doesn't work, but we will change that by connecting the container.

---

## Finding the container ID and connecting it

* Figure out the ID of our alpine container; here are two methods:

  * looking at `/etc/hostname` in the container,

  * running `docker ps -lq` on the host.

* Run the following command on the host:

  ```bash
  $ docker network connect dev `<container_id>`
  ```

---

## Checking what we did

* Try again to `ping es` from the container.

* It should now work correctly:

  ```bash
  / # ping es
  PING es (172.20.0.3): 56 data bytes
  64 bytes from 172.20.0.3: seq=0 ttl=64 time=0.376 ms
  64 bytes from 172.20.0.3: seq=1 ttl=64 time=0.130 ms
  ^C
  ```

* Interrupt it with Ctrl-C.

---

## Looking at the network setup in the container

We can look at the list of network interfaces with `ifconfig`, `ip a`, or `ip l`:

.small[
```bash
/ # ip a
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
18: eth0@if19: <BROADCAST,MULTICAST,UP,LOWER_UP,M-DOWN> mtu 1500 qdisc noqueue state UP
    link/ether 02:42:ac:11:00:02 brd ff:ff:ff:ff:ff:ff
    inet 172.17.0.2/16 brd 172.17.255.255 scope global eth0
       valid_lft forever preferred_lft forever
20: eth1@if21: <BROADCAST,MULTICAST,UP,LOWER_UP,M-DOWN> mtu 1500 qdisc noqueue state UP
    link/ether 02:42:ac:14:00:04 brd ff:ff:ff:ff:ff:ff
    inet 172.20.0.4/16 brd 172.20.255.255 scope global eth1
       valid_lft forever preferred_lft forever
/ #
```
]

Each network connection is materialized with a virtual network interface.

As we can see, we can be connected to multiple networks at the same time.

---

## Disconnecting from a network

* Let's try the symmetrical command to disconnect the container:
  ```bash
  $ docker network disconnect dev <container_id>
  ```

* From now on, if we try to ping `es`, it will not resolve:
  ```bash
  / # ping es
  ping: bad address 'es'
  ```

* Trying to ping the IP address directly won't work either:
  ```bash
  / # ping 172.20.0.3
  ... (nothing happens until we interrupt it with Ctrl-C)
  ```

---

class: extra-details

## Network aliases are scoped per network

* Each network has its own set of network aliases.

* We saw this earlier: `es` resolves to different addresses in `dev` and `prod`.

* If we are connected to multiple networks, the resolver looks up names in each of them
  (as of Docker Engine 18.03, it is the connection order) and stops as soon as the name
  is found.

* Therefore, if we are connected to both `dev` and `prod`, resolving `es` will **not**
  give us the addresses of all the `es` services; but only the ones in `dev` or `prod`.

* However, we can lookup `es.dev` or `es.prod` if we need to.

---

class: extra-details

## Finding out about our networks and names

* We can do reverse DNS lookups on containers' IP addresses.

* If the IP address belongs to a network (other than the default bridge), the result will be:

  ```
  name-or-first-alias-or-container-id.network-name
  ```

* Example:

.small[
```bash
$ docker run -ti --net prod --net-alias hello alpine
/ # apk add --no-cache drill
...
OK: 5 MiB in 13 packages
/ # ifconfig
eth0      Link encap:Ethernet  HWaddr 02:42:AC:15:00:03
          inet addr:`172.21.0.3`  Bcast:172.21.255.255  Mask:255.255.0.0
          UP BROADCAST RUNNING MULTICAST  MTU:1500  Metric:1
...
/ # drill -t ptr `3.0.21.172`.in-addr.arpa
...
;; ANSWER SECTION:
3.0.21.172.in-addr.arpa.	600	IN	PTR	`hello.prod`.
...
```
]

---

class: extra-details

## Building with a custom network

* We can build a Dockerfile with a custom network with `docker build --network NAME`.

* This can be used to check that a build doesn't access the network.

  (But keep in mind that most Dockerfiles will fail,
  <br/>because they need to install remote packages and dependencies!)

* This may be used to access an internal package repository.

  (But try to use a multi-stage build instead, if possible!)

---


class: title

## Working with volumes

![volume](images/title-working-with-volumes.jpg)

---

## Objectives

At the end of this section, you will be able to:

* Create containers holding volumes.

* Share volumes across containers.

* Share a host directory with one or many containers.

---

## Working with volumes

Docker volumes can be used to achieve many things, including:

* Sharing a directory between multiple containers.

* Sharing a directory between the host and a container.

* Sharing a *single file* between the host and a container.

---

## Volumes are special directories in a container

Volumes can be declared in two different ways:

* Within a `Dockerfile`, with a `VOLUME` instruction.

```dockerfile
VOLUME /uploads
```

* On the command-line, with the `-v` flag for `docker run`.

```bash
$ docker run -d -v /uploads myapp
```

In both cases, `/uploads` (inside the container) will be a volume.

---

class: extra-details

## Volumes bypass the copy-on-write system

Volumes act as passthroughs to the host filesystem.

* The I/O performance on a volume is exactly the same as I/O performance
  on the Docker host.

* When you `docker commit`, the content of volumes is not brought into
  the resulting image.

* If a `RUN` instruction in a `Dockerfile` changes the content of a
  volume, those changes are not recorded neither.

* If a container is started with the `--read-only` flag, the volume
  will still be writable (unless the volume is a read-only volume).

---

class: extra-details

## Volumes can be shared across containers

You can start a container with *exactly the same volumes* as another one.

The new container will have the same volumes, in the same directories.

They will contain exactly the same thing, and remain in sync.

Under the hood, they are actually the same directories on the host anyway.

This is done using the `--volumes-from` flag for `docker run`.

We will see an example in the following slides.

---

class: extra-details

## Sharing app server logs with another container

Let's start a Tomcat container:

```bash
$ docker run --name webapp -d -p 8080:8080 -v /usr/local/tomcat/logs tomcat
```

Now, start an `alpine` container accessing the same volume:

```bash
$ docker run --volumes-from webapp alpine sh -c "tail -f /usr/local/tomcat/logs/*"
```

Then, from another window, send requests to our Tomcat container:
```bash
$ curl localhost:8080
```

---

## Volumes exist independently of containers

If a container is stopped or removed, its volumes still exist and are available.

Volumes can be listed and manipulated with `docker volume` subcommands:

```bash
$ docker volume ls
DRIVER              VOLUME NAME
local               5b0b65e4316da67c2d471086640e6005ca2264f3...
local               pgdata-prod
local               pgdata-dev
local               13b59c9936d78d109d094693446e174e5480d973...
```

Some of those volume names were explicit (pgdata-prod, pgdata-dev).

The others (the hex IDs) were generated automatically by Docker.

---

## Naming volumes

* Volumes can be created without a container, then used in multiple containers.

Let's create a couple of volumes directly.

```bash
$ docker volume create webapps
webapps
```

```bash
$ docker volume create logs
logs
```

Volumes are not anchored to a specific path.

---

## Using our named volumes

* Volumes are used with the `-v` option.

* When a host path does not contain a `/`, it is considered a volume name.

Let's start a web server using the two previous volumes.

```bash
$ docker run -d -p 1234:8080 \
         -v logs:/usr/local/tomcat/logs \
         -v webapps:/usr/local/tomcat/webapps \
         tomcat
```

Check that it's running correctly:

```bash
$ curl localhost:1234
... (Tomcat tells us how happy it is to be up and running) ...
```

---

## Using custom "bind-mounts"

In some cases, you want a specific directory on the host to be mapped
inside the container:

* You want to manage storage and snapshots yourself.

* You have a separate disk with better performance (SSD) or resiliency (EBS)
  than the system disk, and you want to put important data on that disk.

* You want to share your source directory between your host 


```bash
$ docker run -d -v /path/on/the/host:/path/in/container image ...
```

---

class: extra-details

## Migrating data with `--volumes-from`

The `--volumes-from` option tells Docker to re-use all the volumes
of an existing container.

* Scenario: migrating from Redis 2.8 to Redis 3.0.

* We have a container (`myredis`) running Redis 2.8.

* Stop the `myredis` container.

* Start a new container, using the Redis 3.0 image, and the `--volumes-from` option.

* The new container will inherit the data of the old one.

* Newer containers can use `--volumes-from` too.

* Doesn't work across servers, so not usable in clusters (Swarm, Kubernetes).

---

class: extra-details

## Data migration in practice

Let's create a Redis container.

```bash
$ docker run -d --name redis28 redis:2.8
```

Connect to the Redis container and set some data.

```bash
$ docker run -ti --link redis28:redis busybox telnet redis 6379
```

Issue the following commands:

```bash
SET counter 42
INFO server
SAVE
QUIT
```

---

class: extra-details

## Upgrading Redis

Stop the Redis container.

```bash
$ docker stop redis28
```

Start the new Redis container.

```bash
$ docker run -d --name redis30 --volumes-from redis28 redis:3.0
```

---

class: extra-details

## Testing the new Redis

Connect to the Redis container and see our data.

```bash
docker run -ti --link redis30:redis busybox telnet redis 6379
```

Issue a few commands.

```bash
GET counter
INFO server
QUIT
```

---

## Volumes lifecycle

* When you remove a container, its volumes are kept around.

* You can list them with `docker volume ls`.

* You can access them by creating a container with `docker run -v`.

* You can remove them with `docker volume rm` or `docker system prune`.

Ultimately, _you_ are the one responsible for logging,
monitoring, and backup of your volumes.

---

## Checking volumes defined by an image

Wondering if an image has volumes? Just use `docker inspect`:

```bash
$ # docker inspect training/datavol
[{
  "config": {
    . . .
    "Volumes": {
        "/var/webapp": {}
    },
    . . .
}]
```

---

## Checking volumes used by a container

To look which paths are actually volumes, and to what they are bound,
use `docker inspect` (again):

```bash
$ docker inspect <yourContainerID>
[{
  "ID": "<yourContainerID>",
. . .
  "Volumes": {
     "/var/webapp": "/var/lib/docker/vfs/dir/f4280c5b6207ed531efd4cc673ff620cef2a7980f747dbbcca001db61de04468"
  },
  "VolumesRW": {
     "/var/webapp": true
  },
}]
```

* We can see that our volume is present on the file system of the Docker host.

---

## Sharing a single file

The same `-v` flag can be used to share a single file (instead of a directory).

One of the most interesting examples is to share the Docker control socket.

```bash
$ docker run -it -v /var/run/docker.sock:/var/run/docker.sock docker sh
```

From that container, you can now run `docker` commands communicating with
the Docker Engine running on the host. Try `docker ps`!

---

class: extra-details

## Volume plugins

You can install plugins to manage volumes backed by particular storage systems,
or providing extra features. For instance:

* [REX-Ray](https://rexray.io/) - create and manage volumes backed by an enterprise storage system (e.g.
  SAN or NAS), or by cloud block stores (e.g. EBS, EFS).

* [Portworx](https://portworx.com/) - provides distributed block store for containers.

* [Gluster](https://www.gluster.org/) - open source software-defined distributed storage that can scale
  to several petabytes. It provides interfaces for object, block and file storage.

* and much more at the [Docker Store](https://store.docker.com/search?category=volume&q=&type=plugin)!

---

## Volumes vs. Mounts

* Since Docker 17.06, a new options is available: `--mount`.

* It offers a new, richer syntax to manipulate data in containers.

* It makes an explicit difference between:

  - volumes (identified with a unique name, managed by a storage plugin),

  - bind mounts (identified with a host path, not managed).

* The former `-v` / `--volume` option is still usable.

---

## `--mount` syntax

Binding a host path to a container path:

```bash
$ docker run \
  --mount type=bind,source=/path/on/host,target=/path/in/container alpine
```

Mounting a volume to a container path:

```bash
$ docker run \
  --mount source=myvolume,target=/path/in/container alpine
```

Mounting a tmpfs (in-memory, for temporary files):

```bash
$ docker run \
  --mount type=tmpfs,destination=/path/in/container,tmpfs-size=1000000 alpine
```

---

## Section summary

We've learned how to:

* Create and manage volumes.

* Share volumes across containers.

* Share a host directory with one or many containers.
