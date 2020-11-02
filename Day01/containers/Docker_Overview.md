# Docker Overview

* The software industry has changed

* Before:
  * monolithic applications
  * long development cycles
  * single environment
  * slowly scaling up

* Now:
  * decoupled services
  * fast, iterative improvements
  * multiple environments
  * quickly scaling out

---

## Deployment becomes very complex

* Many different stacks:
  * languages
  * frameworks
  * databases

* Many different targets:
  * individual development environments
  * pre-production, QA, staging...
  * production: on prem, cloud, hybrid

---

class: pic

## The deployment problem

![problem](images/shipping-software-problem.png)

---

class: pic

## The matrix from hell

![matrix](images/shipping-matrix-from-hell.png)

---

class: pic

## The parallel with the shipping industry

![history](images/shipping-industry-problem.png)

---

class: pic

## Intermodal shipping containers

![shipping](images/shipping-industry-solution.png)

---

class: pic

## A new shipping ecosystem

![shipeco](images/shipping-indsutry-results.png)

---

class: pic

## A shipping container system for applications

![shipapp](images/shipping-software-solution.png)

---

class: pic

## Eliminate the matrix from hell

![elimatrix](images/shipping-matrix-solved.png)

---

## Results

* [Dev-to-prod reduced from 9 months to 15 minutes (ING)](
  https://www.docker.com/sites/default/files/CS_ING_01.25.2015_1.pdf)

* [Continuous integration job time reduced by more than 60% (BBC)](
  https://www.docker.com/sites/default/files/CS_BBCNews_01.25.2015_1.pdf)

* [Deploy 100 times a day instead of once a week (GILT)](
  https://www.docker.com/sites/default/files/CS_Gilt%20Groupe_03.18.2015_0.pdf)

* [70% infrastructure consolidation (MetLife)](
  https://www.docker.com/customers/metlife-transforms-customer-experience-legacy-and-microservices-mashup)

* [60% infrastructure consolidation (Intesa Sanpaolo)](
  https://blog.docker.com/2017/11/intesa-sanpaolo-builds-resilient-foundation-banking-docker-enterprise-edition/)

* [14x application density; 60% of legacy datacenter migrated in 4 months (GE Appliances)](
  https://www.docker.com/customers/ge-uses-docker-enable-self-service-their-developers)

* etc.

---

## Escape dependency hell

1. Write installation instructions into an `INSTALL.txt` file

2. Turn this file into a `Dockerfile`, test it on your machine

3. If the Dockerfile builds on your machine, it will build *anywhere*

4. Rejoice as you escape dependency hell and "works on my machine"

Never again "worked in dev - ops problem now!"

---

## On-board developers and contributors rapidly

1. Write Dockerfiles for your application components

2. Use pre-made images from the Docker Hub (mysql, redis...)

3. Describe your stack with a Compose file

4. On-board somebody with two commands:

```bash
git clone ...
docker-compose up
```

With this, you can create development, integration, QA environments in minutes!

---

class: extra-details

## Implement reliable CI easily

1. Build test environment with a Dockerfile or Compose file

2. For each test run, stage up a new container or stack

3. Each run is now in a clean environment

4. No pollution from previous tests

Way faster and cheaper than creating VMs each time!

---

class: extra-details

## Use container images as build artefacts

1. Build your app from Dockerfiles

2. Store the resulting images in a registry

3. Keep them forever (or as long as necessary)

4. Test those images in QA, CI, integration...

5. Run the same images in production

6. Something goes wrong? Rollback to previous image

7. Investigating old regression? Old image has your back!

Images contain all the libraries, dependencies, etc. needed to run the app.

---

class: extra-details

## Decouple "plumbing" from application logic

1. Write your code to connect to named services ("db", "api"...)

2. Use Compose to start your stack

3. Docker will setup per-container DNS resolver for those names

4. You can now scale, add load balancers, replication ... without changing your code

Note: this is not covered in this intro level workshop!

---

class: extra-details

## What did Docker bring to the table?

### Docker before/after

---

class: extra-details

## Formats and APIs, before Docker

* No standardized exchange format.
  <br/>(No, a rootfs tarball is *not* a format!)

* Containers are hard to use for developers.
  <br/>(Where's the equivalent of `docker run debian`?)

* As a result, they are *hidden* from the end users.

* No re-usable components, APIs, tools.
  <br/>(At best: VM abstractions, e.g. libvirt.)

Analogy: 

* Shipping containers are not just steel boxes.
* They are steel boxes that are a standard size, with the same hooks and holes.

---

class: extra-details

## Formats and APIs, after Docker

* Standardize the container format, because containers were not portable.

* Make containers easy to use for developers.

* Emphasis on re-usable components, APIs, ecosystem of standard tools.

* Improvement over ad-hoc, in-house, specific tools.

---

class: extra-details

## Shipping, before Docker

* Ship packages: deb, rpm, gem, jar, homebrew...

* Dependency hell.

* "Works on my machine."

* Base deployment often done from scratch (debootstrap...) and unreliable.

---

class: extra-details

## Shipping, after Docker

* Ship container images with all their dependencies.

* Images are bigger, but they are broken down into layers.

* Only ship layers that have changed.

* Save disk, network, memory usage.

---

class: extra-details

## Example

Layers:

* CentOS
* JRE
* Tomcat
* Dependencies
* Application JAR
* Configuration

---

class: extra-details

## Devs vs Ops, before Docker

* Drop a tarball (or a commit hash) with instructions.

* Dev environment very different from production.

* Ops don't always have a dev environment themselves ...

* ... and when they do, it can differ from the devs'.

* Ops have to sort out differences and make it work ...

* ... or bounce it back to devs.

* Shipping code causes frictions and delays.

---

class: extra-details

## Devs vs Ops, after Docker

* Drop a container image or a Compose file.

* Ops can always run that container image.

* Ops can always run that Compose file.

* Ops still have to adapt to prod environment,
  but at least they have a reference point.

* Ops have tools allowing to use the same image
  in dev and prod.

* Devs can be empowered to make releases themselves
  more easily.

---

## Our training environment

- install Docker locally 

- install Docker on e.g. a cloud VM

- use https://www.play-with-docker.com/ to instantly get a training environment

---


## What *is* Docker?

- "Installing Docker" really means "Installing the Docker Engine and CLI".

- The Docker Engine is a daemon (a service running in the background).

- This daemon manages containers, the same way that a hypervisor manages VMs.

- We interact with the Docker Engine by using the Docker CLI.

- The Docker CLI and the Docker Engine communicate through an API.

- There are many other programs and client libraries which use that API.

---

## Why don't we run Docker locally?

- We are going to download container images and distribution packages.

- This could put a bit of stress on the local machines and slow us down.

- Instead, we use a remote VM environment that has a good connectivity

- In some rare cases, installing Docker locally is challenging:

  - no administrator/root access (computer managed by strict corp IT)

  - 32-bit CPU or OS

  - old OS version (e.g. CentOS 6, OSX pre-Yosemite, Windows 7)

- It's better to spend time learning containers than fiddling with the installer!

---


class: title

## Installing Docker

![install](images/title-installing-docker.jpg)

---

## Objectives

At the end of this lesson, you will know:

* How to install Docker.

* When to use `sudo` when running Docker commands.

---

## Installing Docker

There are many ways to install Docker.

We can arbitrarily distinguish:

* Installing Docker on an existing Linux machine (physical or VM)

* Installing Docker on macOS or Windows

* Installing Docker on a fleet of cloud VMs

---

## Installing Docker on Linux

* The recommended method is to install the packages supplied by Docker Inc :

  - add Docker Inc.'s package repositories to your system configuration

  - install the Docker Engine

* Detailed installation instructions (distro by distro) are available on:

  https://docs.docker.com/engine/installation/

* You can also install from binaries (if your distro is not supported):

  https://docs.docker.com/engine/installation/linux/docker-ce/binaries/

* To quickly setup a dev environment, Docker provides a convenience install script:

  ```bash
  curl -fsSL get.docker.com | sh
  ```

---

class: extra-details

## Docker Inc. packages vs distribution packages

* Docker Inc. releases new versions monthly (edge) and quarterly (stable)

* Releases are immediately available on Docker Inc.'s package repositories

* Linux distros don't always update to the latest Docker version

  (Sometimes, updating would break their guidelines for major/minor upgrades)

* Sometimes, some distros have carried packages with custom patches

* Sometimes, these patches added critical security bugs ☹

* Installing through Docker Inc.'s repositories is a bit of extra work …

  … but it is generally worth it!

---

## Docker Desktop

* Special Docker edition available for Mac and Windows

* Integrates well with the host OS:

  * installed like normal user applications on the host

  * provides user-friendly GUI to edit Docker configuration and settings

* Only support running one Docker VM at a time ...

  ... but we can use `docker-machine`, VirtualBox, etc. to get a cluster.

---

## Running Docker on macOS and Windows

When you execute `docker version` from the terminal:

* the CLI connects to the Docker Engine over a standard socket,
* the Docker Engine is, in fact, running in a VM,
* ... but the CLI doesn't know or care about that,
* the CLI sends a request using the REST API,
* the Docker Engine in the VM processes the request,
* the CLI gets the response and displays it to you.

All communication with the Docker Engine happens over the API.

This will also allow to use remote Engines exactly as if they were local.

---

class: title

## Our first containers

![Colorful plastic tubs](images/title-our-first-containers.jpg)

---

## Objectives

At the end of this lesson, you will have:

* Seen Docker in action.

* Started your first containers.

---

## Hello World

In your Docker environment, just run the following command:

```bash
$ docker run busybox echo hello world
hello world
```

(If your Docker install is brand new, you will also see a few extra lines,
corresponding to the download of the `busybox` image.)

---

## That was our first container!

* We used one of the smallest, simplest images available: `busybox`.

* `busybox` is typically used in embedded systems (phones, routers...)

* We ran a single process and echo'ed `hello world`.

---

## A more useful container

Let's run a more exciting container:

```bash
$ docker run -it ubuntu
root@04c0bb0a6c07:/#
```

* This is a brand new container.

* It runs a bare-bones, no-frills `ubuntu` system.

* `-it` is shorthand for `-i -t`.

  * `-i` tells Docker to connect us to the container's stdin.

  * `-t` tells Docker that we want a pseudo-terminal.

---

## Do something in our container

Try to run `figlet` in our container.

```bash
root@04c0bb0a6c07:/# figlet hello
bash: figlet: command not found
```

Alright, we need to install it.

---

## Install a package in our container

We want `figlet`, so let's install it:

```bash
root@04c0bb0a6c07:/# apt-get update
...
Fetched 1514 kB in 14s (103 kB/s)
Reading package lists... Done
root@04c0bb0a6c07:/# apt-get install figlet
Reading package lists... Done
...
```

One minute later, `figlet` is installed!

---

## Try to run our freshly installed program

The `figlet` program takes a message as parameter.

```bash
root@04c0bb0a6c07:/# figlet hello
 _          _ _       
| |__   ___| | | ___  
| '_ \ / _ \ | |/ _ \ 
| | | |  __/ | | (_) |
|_| |_|\___|_|_|\___/ 
```

Beautiful! .emoji[😍]

---

class: in-person

## Counting packages in the container

Let's check how many packages are installed there.

```bash
root@04c0bb0a6c07:/# dpkg -l | wc -l
190
```

* `dpkg -l` lists the packages installed in our container

* `wc -l` counts them

How many packages do we have on our host?

---

class: in-person

## Counting packages on the host

Exit the container by logging out of the shell, like you would usually do.

(E.g. with `^D` or `exit`)

```bash
root@04c0bb0a6c07:/# exit
```

Now, try to:

* run `dpkg -l | wc -l`. How many packages are installed?

* run `figlet`. Does that work?

---

class: self-paced

## Comparing the container and the host

Exit the container by logging out of the shell, with `^D` or `exit`.

Now try to run `figlet`. Does that work?

(It shouldn't; except if, by coincidence, you are running on a machine where figlet was installed before.)

---

## Host and containers are independent things

* We ran an `ubuntu` container on an Linux/Windows/macOS host.

* They have different, independent packages.

* Installing something on the host doesn't expose it to the container.

* And vice-versa.

* Even if both the host and the container have the same Linux distro!

* We can run *any container* on *any host*.

  (One exception: Windows containers cannot run on Linux machines; at least not yet.)

---

## Where's our container?

* Our container is now in a *stopped* state.

* It still exists on disk, but all compute resources have been freed up.

* We will see later how to get back to that container.

---

## Starting another container

What if we start a new container, and try to run `figlet` again?
 
```bash
$ docker run -it ubuntu
root@b13c164401fb:/# figlet
bash: figlet: command not found
```

* We started a *brand new container*.

* The basic Ubuntu image was used, and `figlet` is not here.

---

## Where's my container?

* Can we reuse that container that we took time to customize?

  *We can, but that's not the default workflow with Docker.*

* What's the default workflow, then?

  *Always start with a fresh container.*
  <br/>
  *If we need something installed in our container, build a custom image.*

* That seems complicated!

  *We'll see that it's actually pretty easy!*

* And what's the point?

  *This puts a strong emphasis on automation and repeatability. Let's see why ...*

---

## Pets vs. Cattle

* In the "pets vs. cattle" metaphor, there are two kinds of servers.

* Pets:

  * have distinctive names and unique configurations

  * when they have an outage, we do everything we can to fix them

* Cattle:

  * have generic names (e.g. with numbers) and generic configuration

  * configuration is enforced by configuration management, golden images ...

  * when they have an outage, we can replace them immediately with a new server

* What's the connection with Docker and containers?

---

## Local development environments

* When we use local VMs (with e.g. VirtualBox or VMware), our workflow looks like this:

  * create VM from base template (Ubuntu, CentOS...)

  * install packages, set up environment

  * work on project

  * when done, shut down VM

  * next time we need to work on project, restart VM as we left it

  * if we need to tweak the environment, we do it live

* Over time, the VM configuration evolves, diverges.

* We don't have a clean, reliable, deterministic way to provision that environment.

---

## Local development with Docker

* With Docker, the workflow looks like this:

  * create container image with our dev environment

  * run container with that image

  * work on project

  * when done, shut down container

  * next time we need to work on project, start a new container

  * if we need to tweak the environment, we create a new image

* We have a clear definition of our environment, and can share it reliably with others.

* Let's see in the next chapters how to bake a custom image with `figlet`!

???

:EN:- Running our first container
:FR:- Lancer nos premiers conteneurs

---

class: title

## Background containers

![Background containers](images/title-background-containers.jpg)

---

## Objectives

Our first containers were *interactive*.

We will now see how to:

* Run a non-interactive container.
* Run a container in the background.
* List running containers.
* Check the logs of a container.
* Stop a container.
* List stopped containers.

---

## A non-interactive container

We will run a small custom container.

This container just displays the time every second.

```bash
$ docker run ahmedgabercod/clock
Fri Feb 20 00:28:53 UTC 2015
Fri Feb 20 00:28:54 UTC 2015
Fri Feb 20 00:28:55 UTC 2015
...
```

* This container will run forever.
* To stop it, press `^C`.
* Docker has automatically downloaded the image `ahmedgabercod/clock`.
* This image is a user image, created by `ahmedgabercod`.
* We will hear more about user images (and other types of images) later.

---

## Run a container in the background

Containers can be started in the background, with the `-d` flag (daemon mode):

```bash
$ docker run -d ahmedgabercod/clock
47d677dcfba4277c6cc68fcaa51f932b544cab1a187c853b7d0caf4e8debe5ad
```

* We don't see the output of the container.
* But don't worry: Docker collects that output and logs it!
* Docker gives us the ID of the container.

---

## List running containers

How can we check that our container is still running?

With `docker ps`, just like the UNIX `ps` command, lists running processes.

```bash
$ docker ps
CONTAINER ID  IMAGE           ...  CREATED        STATUS        ...
47d677dcfba4  ahmedgabercod/clock  ...  2 minutes ago  Up 2 minutes  ...
```

Docker tells us:

* The (truncated) ID of our container.
* The image used to start the container.
* That our container has been running (`Up`) for a couple of minutes.
* Other information (COMMAND, PORTS, NAMES) that we will explain later.

---

## Starting more containers

Let's start two more containers.

```bash
$ docker run -d ahmedgabercod/clock
57ad9bdfc06bb4407c47220cf59ce21585dce9a1298d7a67488359aeaea8ae2a
```

```bash
$ docker run -d ahmedgabercod/clock
068cc994ffd0190bbe025ba74e4c0771a5d8f14734af772ddee8dc1aaf20567d
```

Check that `docker ps` correctly reports all 3 containers.

---

## Viewing only the last container started

When many containers are already running, it can be useful to
see only the last container that was started.

This can be achieved with the `-l` ("Last") flag:

```bash
$ docker ps -l
CONTAINER ID  IMAGE           ...  CREATED        STATUS        ...
068cc994ffd0  ahmedgabercod/clock  ...  2 minutes ago  Up 2 minutes  ...
```

---

## View only the IDs of the containers

Many Docker commands will work on container IDs: `docker stop`, `docker rm`...

If we want to list only the IDs of our containers (without the other columns
or the header line),
we can use the `-q` ("Quiet", "Quick") flag:

```bash
$ docker ps -q
068cc994ffd0
57ad9bdfc06b
47d677dcfba4
```

---

## Combining flags

We can combine `-l` and `-q` to see only the ID of the last container started:

```bash
$ docker ps -lq
068cc994ffd0
```

At a first glance, it looks like this would be particularly useful in scripts.

However, if we want to start a container and get its ID in a reliable way,
it is better to use `docker run -d`, which we will cover in a bit.

(Using `docker ps -lq` is prone to race conditions: what happens if someone
else, or another program or script, starts another container just before
we run `docker ps -lq`?)

---

## View the logs of a container

We told you that Docker was logging the container output.

Let's see that now.

```bash
$ docker logs 068
Fri Feb 20 00:39:52 UTC 2015
Fri Feb 20 00:39:53 UTC 2015
...
```

* We specified a *prefix* of the full container ID.
* You can, of course, specify the full ID.
* The `logs` command will output the *entire* logs of the container.
  <br/>(Sometimes, that will be too much. Let's see how to address that.)

---

## View only the tail of the logs

To avoid being spammed with eleventy pages of output,
we can use the `--tail` option:

```bash
$ docker logs --tail 3 068
Fri Feb 20 00:55:35 UTC 2015
Fri Feb 20 00:55:36 UTC 2015
Fri Feb 20 00:55:37 UTC 2015
```

* The parameter is the number of lines that we want to see.

---

## Follow the logs in real time

Just like with the standard UNIX command `tail -f`, we can
follow the logs of our container:

```bash
$ docker logs --tail 1 --follow 068
Fri Feb 20 00:57:12 UTC 2015
Fri Feb 20 00:57:13 UTC 2015
^C
```

* This will display the last line in the log file.
* Then, it will continue to display the logs in real time.
* Use `^C` to exit.

---

## Stop our container

There are two ways we can terminate our detached container.

* Killing it using the `docker kill` command.
* Stopping it using the `docker stop` command.

The first one stops the container immediately, by using the
`KILL` signal.

The second one is more graceful. It sends a `TERM` signal,
and after 10 seconds, if the container has not stopped, it
sends `KILL.`

Reminder: the `KILL` signal cannot be intercepted, and will
forcibly terminate the container.

---

## Stopping our containers

Let's stop one of those containers:

```bash
$ docker stop 47d6
47d6
```

This will take 10 seconds:

* Docker sends the TERM signal;
* the container doesn't react to this signal
  (it's a simple Shell script with no special
  signal handling);
* 10 seconds later, since the container is still
  running, Docker sends the KILL signal;
* this terminates the container.

---

## Killing the remaining containers

Let's be less patient with the two other containers:

```bash
$ docker kill 068 57ad
068
57ad
```

The `stop` and `kill` commands can take multiple container IDs.

Those containers will be terminated immediately (without
the 10-second delay).

Let's check that our containers don't show up anymore:

```bash
$ docker ps
```

---

## List stopped containers

We can also see stopped containers, with the `-a` (`--all`) option.

```bash
$ docker ps -a
CONTAINER ID  IMAGE           ...  CREATED      STATUS
068cc994ffd0  ahmedgabercod/clock  ...  21 min. ago  Exited (137) 3 min. ago
57ad9bdfc06b  ahmedgabercod/clock  ...  21 min. ago  Exited (137) 3 min. ago
47d677dcfba4  ahmedgabercod/clock  ...  23 min. ago  Exited (137) 3 min. ago
5c1dfd4d81f1  ahmedgabercod/clock  ...  40 min. ago  Exited (0) 40 min. ago
b13c164401fb  ubuntu          ...  55 min. ago  Exited (130) 53 min. ago
```

???

:EN:- Foreground and background containers
:FR:- Exécution interactive ou en arrière-plan

---

## Restarting and attaching to containers

We have started containers in the foreground, and in the background.

In this chapter, we will see how to:

* Put a container in the background.
* Attach to a background container to bring it to the foreground.
* Restart a stopped container.

---

## Background and foreground

The distinction between foreground and background containers is arbitrary.

From Docker's point of view, all containers are the same.

All containers run the same way, whether there is a client attached to them or not.

It is always possible to detach from a container, and to reattach to a container.

Analogy: attaching to a container is like plugging a keyboard and screen to a physical server.

---

## Detaching from a container (Linux/macOS)

* If you have started an *interactive* container (with option `-it`), you can detach from it.

* The "detach" sequence is `^P^Q`.

* Otherwise you can detach by killing the Docker client.
  
  (But not by hitting `^C`, as this would deliver `SIGINT` to the container.)

What does `-it` stand for?

* `-t` means "allocate a terminal."
* `-i` means "connect stdin to the terminal."

---

## Detaching cont. (Win PowerShell and cmd.exe)

* Docker for Windows has a different detach experience due to shell features.

* `^P^Q` does not work.

* `^C` will detach, rather than stop the container.

* Using Bash, Subsystem for Linux, etc. on Windows behaves like Linux/macOS shells.

* Both PowerShell and Bash work well in Win 10; just be aware of differences.

---

class: extra-details

## Specifying a custom detach sequence

* You don't like `^P^Q`? No problem!
* You can change the sequence with `docker run --detach-keys`.
* This can also be passed as a global option to the engine.

Start a container with a custom detach command:

```bash
$ docker run -ti --detach-keys ctrl-x,x ahmedgabercod/clock
```

Detach by hitting `^X x`. (This is ctrl-x then x, not ctrl-x twice!)

Check that our container is still running:

```bash
$ docker ps -l
```

---

class: extra-details

## Attaching to a container

You can attach to a container:

```bash
$ docker attach <containerID>
```

* The container must be running.
* There *can* be multiple clients attached to the same container.
* If you don't specify `--detach-keys` when attaching, it defaults back to `^P^Q`.

Try it on our previous container:

```bash
$ docker attach $(docker ps -lq)
```

Check that `^X x` doesn't work, but `^P ^Q` does.

---

## Detaching from non-interactive containers

* **Warning:** if the container was started without `-it`...

  * You won't be able to detach with `^P^Q`.
  * If you hit `^C`, the signal will be proxied to the container.

* Remember: you can always detach by killing the Docker client.

---

## Checking container output

* Use `docker attach` if you intend to send input to the container.

* If you just want to see the output of a container, use `docker logs`.

```bash
$ docker logs --tail 1 --follow <containerID>
```

---

## Restarting a container

When a container has exited, it is in stopped state.

It can then be restarted with the `start` command.

```bash
$ docker start <yourContainerID>
```

The container will be restarted using the same options you launched it
with.

You can re-attach to it if you want to interact with it:

```bash
$ docker attach <yourContainerID>
```

Use `docker ps -a` to identify the container ID of a previous `ahmedgabercod/clock` container,
and try those commands.

---

## Attaching to a REPL

* REPL = Read Eval Print Loop

* Shells, interpreters, TUI ...

* Symptom: you `docker attach`, and see nothing

* The REPL doesn't know that you just attached, and doesn't print anything

* Try hitting `^L` or `Enter`

---

class: extra-details

## SIGWINCH

* When you `docker attach`, the Docker Engine sends SIGWINCH signals to the container.

* SIGWINCH = WINdow CHange; indicates a change in window size.

* This will cause some CLI and TUI programs to redraw the screen.

* But not all of them.

---

class: title

## Naming and inspecting containers

![Markings on container door](images/title-naming-and-inspecting-containers.jpg)

---

## Objectives

Docker concept: container *naming*.

Naming allows us to:

* Reference easily a container.

* Ensure unicity of a specific container.

We will also see the `inspect` command, which gives a lot of details about a container.

---

## Naming our containers

So far, we have referenced containers with their ID.

We have copy-pasted the ID, or used a shortened prefix.

But each container can also be referenced by its name.

If a container is named `thumbnail-worker`, I can do:

```bash
$ docker logs thumbnail-worker
$ docker stop thumbnail-worker
etc.
```

---

## Default names

When we create a container, if we don't give a specific
name, Docker will pick one for us.

It will be the concatenation of:

* A mood (furious, goofy, suspicious, boring...)

* The name of a famous inventor (tesla, darwin, wozniak...)

Examples: `happy_curie`, `clever_hopper`, `jovial_lovelace` ...

---

## Specifying a name

You can set the name of the container when you create it.

```bash
$ docker run --name ticktock ahmedgabercod/clock
```

If you specify a name that already exists, Docker will refuse
to create the container.

This lets us enforce unicity of a given resource.

---

## Renaming containers

* You can rename containers with `docker rename`.

* This allows you to "free up" a name without destroying the associated container.

---

## Inspecting a container

The `docker inspect` command will output a very detailed JSON map.

```bash
$ docker inspect <containerID>
[{
...
(many pages of JSON here)
...
```

There are multiple ways to consume that information.

---

## Parsing JSON with the Shell

* You *could* grep and cut or awk the output of `docker inspect`.

* Please, don't.

* It's painful.

* If you really must parse JSON from the Shell, use JQ! (It's great.)

```bash
$ docker inspect <containerID> | jq .
```

* We will see a better solution which doesn't require extra tools.

---

## Using `--format`

You can specify a format string, which will be parsed by 
Go's text/template package.

```bash
$ docker inspect --format '{{ json .Created }}' <containerID>
"2015-02-24T07:21:11.712240394Z"
```

* The generic syntax is to wrap the expression with double curly braces.

* The expression starts with a dot representing the JSON object.

* Then each field or member can be accessed in dotted notation syntax.

* The optional `json` keyword asks for valid JSON output.
  <br/>(e.g. here it adds the surrounding double-quotes.)

---

## Labels

* Labels allow to attach arbitrary metadata to containers.

* Labels are key/value pairs.

* They are specified at container creation.

* You can query them with `docker inspect`.

* They can also be used as filters with some commands (e.g. `docker ps`).

---

## Using labels

Let's create a few containers with a label `owner`.

```bash
docker run -d -l owner=alice nginx
docker run -d -l owner=bob nginx
docker run -d -l owner nginx
```

We didn't specify a value for the `owner` label in the last example.

This is equivalent to setting the value to be an empty string.

---

## Querying labels

We can view the labels with `docker inspect`.

```bash
$ docker inspect $(docker ps -lq) | grep -A3 Labels
            "Labels": {
                "maintainer": "NGINX Docker Maintainers <docker-maint@nginx.com>",
                "owner": ""
            },
```

We can use the `--format` flag to list the value of a label.

```bash
$ docker inspect $(docker ps -q) --format 'OWNER={{.Config.Labels.owner}}'
```

---

## Using labels to select containers

We can list containers having a specific label.

```bash
$ docker ps --filter label=owner
```

Or we can list containers having a specific label with a specific value.

```bash
$ docker ps --filter label=owner=alice
```

---

## Use-cases for labels


* HTTP vhost of a web app or web service.

  (The label is used to generate the configuration for NGINX, HAProxy, etc.)

* Backup schedule for a stateful service.

  (The label is used by a cron job to determine if/when to backup container data.)

* Service ownership.

  (To determine internal cross-billing, or who to page in case of outage.)

* etc.

---

class: title

## Getting inside a container

![Person standing inside a container](images/getting-inside.png)

---

## Objectives

On a traditional server or VM, we sometimes need to:

* log into the machine (with SSH or on the console),

* analyze the disks (by removing them or rebooting with a rescue system).

In this chapter, we will see how to do that with containers.

---

## Getting a shell

Every once in a while, we want to log into a machine.

In an perfect world, this shouldn't be necessary.

* You need to install or update packages (and their configuration)?

  Use configuration management. (e.g. Ansible, Chef, Puppet, Salt...)

* You need to view logs and metrics?

  Collect and access them through a centralized platform.

In the real world, though ... we often need shell access!

---

## Not getting a shell

Even without a perfect deployment system, we can do many operations without getting a shell.

* Installing packages can (and should) be done in the container image.

* Configuration can be done at the image level, or when the container starts.

* Dynamic configuration can be stored in a volume (shared with another container).

* Logs written to stdout are automatically collected by the Docker Engine.

* Other logs can be written to a shared volume.

* Process information and metrics are visible from the host.

_Let's save logging, volumes ... for later, but let's have a look at process information!_

---

## Viewing container processes from the host

If you run Docker on Linux, container processes are visible on the host.

```bash
$ ps faux | less
```

* Scroll around the output of this command.

* You should see the `ahmedgabercod/clock` container.

* A containerized process is just like any other process on the host.

* We can use tools like `lsof`, `strace`, `gdb` ... To analyze them.

---

class: extra-details

## What's the difference between a container process and a host process?

* Each process (containerized or not) belongs to *namespaces* and *cgroups*.

* The namespaces and cgroups determine what a process can "see" and "do".

* Analogy: each process (containerized or not) runs with a specific UID (user ID).

* UID=0 is root, and has elevated privileges. Other UIDs are normal users.

_We will give more details about namespaces and cgroups later._

---

## Getting a shell in a running container

* Sometimes, we need to get a shell anyway.

* We _could_ run some SSH server in the container ...

* But it is easier to use `docker exec`.

```bash
$ docker exec -ti ticktock sh
```

* This creates a new process (running `sh`) _inside_ the container.

* This can also be done "manually" with the tool `nsenter`.

---

## Caveats

* The tool that you want to run needs to exist in the container.

* Some tools (like `ip netns exec`) let you attach to _one_ namespace at a time.

  (This lets you e.g. setup network interfaces, even if you don't have `ifconfig` or `ip` in the container.)

* Most importantly: the container needs to be running.

* What if the container is stopped or crashed?

---

## Getting a shell in a stopped container

* A stopped container is only _storage_ (like a disk drive).

* We cannot SSH into a disk drive or USB stick!

* We need to connect the disk to a running machine.

* How does that translate into the container world?

---

## Analyzing a stopped container

As an exercise, we are going to try to find out what's wrong with `ahmedgabercod/crashtest`.

```bash
docker run ahmedgabercod/crashtest
```

The container starts, but then stops immediately, without any output.

What would MacGyver&trade; do?

First, let's check the status of that container.

```bash
docker ps -l
```

---

## Viewing filesystem changes

* We can use `docker diff` to see files that were added / changed / removed.

```bash
docker diff <container_id>
```

* The container ID was shown by `docker ps -l`.

* We can also see it with `docker ps -lq`.

* The output of `docker diff` shows some interesting log files!

---

## Accessing files

* We can extract files with `docker cp`.

```bash
docker cp <container_id>:/var/log/nginx/error.log .
```

* Then we can look at that log file.

```bash
cat error.log
```

(The directory `/run/nginx` doesn't exist.)

---

class: extra-details

## Exploring a crashed container

* We can restart a container with `docker start` ...

* ... But it will probably crash again immediately!

* We cannot specify a different program to run with `docker start`

* But we can create a new image from the crashed container

```bash
docker commit <container_id> debugimage
```

* Then we can run a new container from that image, with a custom entrypoint

```bash
docker run -ti --entrypoint sh debugimage
```

---

class: extra-details

## Obtaining a complete dump

* We can also dump the entire filesystem of a container.

* This is done with `docker export`.

* It generates a tar archive.

```bash
docker export <container_id> | tar tv
```

This will give a detailed listing of the content of the container.

