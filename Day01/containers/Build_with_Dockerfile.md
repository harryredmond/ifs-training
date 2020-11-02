
class: title

# Understanding Docker images

![image](images/title-understanding-docker-images.png)

---

## Objectives

In this section, we will explain:

* What is an image.

* What is a layer.

* The various image namespaces.

* How to search and download images.

* Image tags and when to use them.

---

## What is an image?

* Image = files + metadata

* These files form the root filesystem of our container.

* The metadata can indicate a number of things, e.g.:

  * the author of the image
  * the command to execute in the container when starting it
  * environment variables to be set
  * etc.

* Images are made of *layers*, conceptually stacked on top of each other.

* Each layer can add, change, and remove files and/or metadata.

* Images can share layers to optimize disk usage, transfer times, and memory use.

---

## Example for a Java webapp

Each of the following items will correspond to one layer:

* CentOS base layer
* Packages and configuration files added by our local IT
* JRE
* Tomcat
* Our application's dependencies
* Our application code and assets
* Our application configuration

---

class: pic

## The read-write layer

![layers](images/container-layers.jpg)

---

## Differences between containers and images

* An image is a read-only filesystem.

* A container is an encapsulated set of processes,

  running in a read-write copy of that filesystem.

* To optimize container boot time, *copy-on-write* is used
  instead of regular copy.

* `docker run` starts a container from a given image.

---

class: pic

## Multiple containers sharing the same image

![layers](images/sharing-layers.jpg)

---

## Images namespaces

There are three namespaces:

* Official images

    e.g. `ubuntu`, `busybox` ...

* User (and organizations) images

    e.g. `ahmedgabercod/clock`

* Self-hosted images

    e.g. `registry.example.com:5000/my-private/image`

Let's explain each of them.

---

## Root namespace

The root namespace is for official images.

They are gated by Docker Inc.

They are generally authored and maintained by third parties.

Those images include:

* Small, "swiss-army-knife" images like busybox.

* Distro images to be used as bases for your builds, like ubuntu, fedora...

* Ready-to-use components and services, like redis, postgresql...

* Over 150 at this point!

---

## User namespace

The user namespace holds images for Docker Hub users and organizations.

For example:

```bash
ahmedgabercod/clock
```

The Docker Hub user is:

```bash
ahmedgabercod
```

The image name is:

```bash
clock
```

---

## Self-hosted namespace

This namespace holds images which are not hosted on Docker Hub, but on third
party registries.

They contain the hostname (or IP address), and optionally the port, of the
registry server.

For example:

```bash
localhost:5000/wordpress
```

* `localhost:5000` is the host and port of the registry
* `wordpress` is the name of the image

Other examples:

```bash
quay.io/coreos/etcd
gcr.io/google-containers/hugo
```

---

## How do you store and manage images?

Images can be stored:

* On your Docker host.
* In a Docker registry.

You can use the Docker client to download (pull) or upload (push) images.

To be more accurate: you can use the Docker client to tell a Docker Engine
to push and pull images to and from a registry.

---

## Showing current images

Let's look at what images are on our host now.

```bash
$ docker images
REPOSITORY       TAG       IMAGE ID       CREATED         SIZE
fedora           latest    ddd5c9c1d0f2   3 days ago      204.7 MB
centos           latest    d0e7f81ca65c   3 days ago      196.6 MB
ubuntu           latest    07c86167cdc4   4 days ago      188 MB
redis            latest    4f5f397d4b7c   5 days ago      177.6 MB
postgres         latest    afe2b5e1859b   5 days ago      264.5 MB
alpine           latest    70c557e50ed6   5 days ago      4.798 MB
debian           latest    f50f9524513f   6 days ago      125.1 MB
busybox          latest    3240943c9ea3   2 weeks ago     1.114 MB
training/namer   latest    902673acc741   9 months ago    289.3 MB
ahmedgabercod/clock   latest    12068b93616f   12 months ago   2.433 MB
```

---

## Searching for images

We cannot list *all* images on a remote registry, but
we can search for a specific keyword:

```bash
$ docker search marathon
NAME                     DESCRIPTION                     STARS  OFFICIAL  AUTOMATED
mesosphere/marathon      A cluster-wide init and co...   105              [OK]
mesoscloud/marathon      Marathon                        31               [OK]
mesosphere/marathon-lb   Script to update haproxy b...   22               [OK]
tobilg/mongodb-marathon  A Docker image to start a ...   4                [OK]
```


* "Stars" indicate the popularity of the image.

* "Official" images are those in the root namespace.

* "Automated" images are built automatically by the Docker Hub.
  <br/>(This means that their build recipe is always available.)

---

## Downloading images

There are two ways to download images.

* Explicitly, with `docker pull`.

* Implicitly, when executing `docker run` and the image is not found locally.

---

## Pulling an image

```bash
$ docker pull debian:jessie
Pulling repository debian
b164861940b8: Download complete
b164861940b8: Pulling image (jessie) from debian
d1881793a057: Download complete
```

* As seen previously, images are made up of layers.

* Docker has downloaded all the necessary layers.

* In this example, `:jessie` indicates which exact version of Debian
  we would like.

  It is a *version tag*.

---

## Image and tags

* Images can have tags.

* Tags define image versions or variants.

* `docker pull ubuntu` will refer to `ubuntu:latest`.

* The `:latest` tag is generally updated often.

---

## When to (not) use tags

Don't specify tags:

* When doing rapid testing and prototyping.
* When experimenting.
* When you want the latest version.

Do specify tags:

* When recording a procedure into a script.
* When going to production.
* To ensure that the same version will be used everywhere.
* To ensure repeatability later.

This is similar to what we would do with `pip install`, `npm install`, etc.

---

## Section summary

We've learned how to:

* Understand images and layers.
* Understand Docker image namespacing.
* Search and download images.

---

class: title

# Building Docker images with a Dockerfile (Basic)

![Construction site with containers](images/title-building-docker-images-with-a-dockerfile.jpg)

---

## Objectives

We will build a container image automatically, with a `Dockerfile`.

At the end of this lesson, you will be able to:

* Write a `Dockerfile`.

* Build an image from a `Dockerfile`.

---

## `Dockerfile` overview

* A `Dockerfile` is a build recipe for a Docker image.

* It contains a series of instructions telling Docker how an image is constructed.

* The `docker build` command builds an image from a `Dockerfile`.

---

## Writing our first `Dockerfile`

Our Dockerfile must be in a **new, empty directory**.

1. Create a directory to hold our `Dockerfile`.

```bash
$ mkdir myimage
```

2. Create a `Dockerfile` inside this directory.

```bash
$ cd myimage
$ vim Dockerfile
```

Of course, you can use any other editor of your choice.

---

## Type this into our Dockerfile...

```dockerfile
FROM ubuntu
RUN apt-get update
RUN apt-get install figlet
```

* `FROM` indicates the base image for our build.

* Each `RUN` line will be executed by Docker during the build.

* Our `RUN` commands **must be non-interactive.**
  <br/>(No input can be provided to Docker during the build.)

* In many cases, we will add the `-y` flag to `apt-get`.

---

## Build it!

Save our file, then execute:

```bash
$ docker build -t figlet .
```

* `-t` indicates the tag to apply to the image.

* `.` indicates the location of the *build context*.

We will talk more about the build context later.

To keep things simple for now: this is the directory where our Dockerfile is located.

---

## What happens when we build the image?

The output of `docker build` looks like this:

.small[
```bash
docker build -t figlet .
Sending build context to Docker daemon  2.048kB
Step 1/3 : FROM ubuntu
 ---> f975c5035748
Step 2/3 : RUN apt-get update
 ---> Running in e01b294dbffd
(...output of the RUN command...)
Removing intermediate container e01b294dbffd
 ---> eb8d9b561b37
Step 3/3 : RUN apt-get install figlet
 ---> Running in c29230d70f9b
(...output of the RUN command...)
Removing intermediate container c29230d70f9b
 ---> 0dfd7a253f21
Successfully built 0dfd7a253f21
Successfully tagged figlet:latest
```
]

* The output of the `RUN` commands has been omitted.
* Let's explain what this output means.

---

## Sending the build context to Docker

```bash
Sending build context to Docker daemon 2.048 kB
```

* The build context is the `.` directory given to `docker build`.

* It is sent (as an archive) by the Docker client to the Docker daemon.

* This allows to use a remote machine to build using local files.

* Be careful (or patient) if that directory is big and your link is slow.

* You can speed up the process with a [`.dockerignore`](https://docs.docker.com/engine/reference/builder/#dockerignore-file) file 

  * It tells docker to ignore specific files in the directory

  * Only ignore files that you won't need in the build context!

---

## Executing each step

```bash
Step 2/3 : RUN apt-get update
 ---> Running in e01b294dbffd
(...output of the RUN command...)
Removing intermediate container e01b294dbffd
 ---> eb8d9b561b37
```

* A container (`e01b294dbffd`) is created from the base image.

* The `RUN` command is executed in this container.

* The container is committed into an image (`eb8d9b561b37`).

* The build container (`e01b294dbffd`) is removed.

* The output of this step will be the base image for the next one.

---

## The caching system

If you run the same build again, it will be instantaneous. Why?

* After each build step, Docker takes a snapshot of the resulting image.

* Before executing a step, Docker checks if it has already built the same sequence.

* Docker uses the exact strings defined in your Dockerfile, so:

  * `RUN apt-get install figlet cowsay ` 
    <br/> is different from
    <br/> `RUN apt-get install cowsay figlet`
  
  * `RUN apt-get update` is not re-executed when the mirrors are updated

You can force a rebuild with `docker build --no-cache ...`.

---

## Running the image

The resulting image is not different from the one produced manually.

```bash
$ docker run -ti figlet
root@91f3c974c9a1:/# figlet hello
 _          _ _       
| |__   ___| | | ___  
| '_ \ / _ \ | |/ _ \ 
| | | |  __/ | | (_) |
|_| |_|\___|_|_|\___/ 
```


Yay! .emoji[🎉]

---

## Using image and viewing history

The `history` command lists all the layers composing an image.

For each layer, it shows its creation time, size, and creation command.

When an image was built with a Dockerfile, each layer corresponds to
a line of the Dockerfile.

```bash
$ docker history figlet
IMAGE         CREATED            CREATED BY                     SIZE
f9e8f1642759  About an hour ago  /bin/sh -c apt-get install fi  1.627 MB
7257c37726a1  About an hour ago  /bin/sh -c apt-get update      21.58 MB
07c86167cdc4  4 days ago         /bin/sh -c #(nop) CMD ["/bin   0 B
<missing>     4 days ago         /bin/sh -c sed -i 's/^#\s*\(   1.895 kB
<missing>     4 days ago         /bin/sh -c echo '#!/bin/sh'    194.5 kB
<missing>     4 days ago         /bin/sh -c #(nop) ADD file:b   187.8 MB
```

---

class: extra-details

## Why `sh -c`?

* On UNIX, to start a new program, we need two system calls:

  - `fork()`, to create a new child process;

  - `execve()`, to replace the new child process with the program to run.

* Conceptually, `execve()` works like this:

  `execve(program, [list, of, arguments])`

* When we run a command, e.g. `ls -l /tmp`, something needs to parse the command.

  (i.e. split the program and its arguments into a list.)

* The shell is usually doing that.

  (It also takes care of expanding environment variables and special things like `~`.)

---

class: extra-details

## Why `sh -c`?

* When we do `RUN ls -l /tmp`, the Docker builder needs to parse the command.

* Instead of implementing its own parser, it outsources the job to the shell.

* That's why we see `sh -c ls -l /tmp` in that case.

* But we can also do the parsing jobs ourselves.

* This means passing `RUN` a list of arguments.

* This is called the *exec syntax*.

---

## Shell syntax vs exec syntax

Dockerfile commands that execute something can have two forms:

* plain string, or *shell syntax*:
  <br/>`RUN apt-get install figlet`

* JSON list, or *exec syntax*:
  <br/>`RUN ["apt-get", "install", "figlet"]`

We are going to change our Dockerfile to see how it affects the resulting image.

---

## Using exec syntax in our Dockerfile

Let's change our Dockerfile as follows!

```dockerfile
FROM ubuntu
RUN apt-get update
RUN ["apt-get", "install", "figlet"]
```

Then build the new Dockerfile.

```bash
$ docker build -t figlet .
```

---

## History with exec syntax

Compare the new history:

```bash
$ docker history figlet
IMAGE         CREATED            CREATED BY                     SIZE
27954bb5faaf  10 seconds ago     apt-get install figlet         1.627 MB
7257c37726a1  About an hour ago  /bin/sh -c apt-get update      21.58 MB
07c86167cdc4  4 days ago         /bin/sh -c #(nop) CMD ["/bin   0 B
<missing>     4 days ago         /bin/sh -c sed -i 's/^#\s*\(   1.895 kB
<missing>     4 days ago         /bin/sh -c echo '#!/bin/sh'    194.5 kB
<missing>     4 days ago         /bin/sh -c #(nop) ADD file:b   187.8 MB
```

* Exec syntax specifies an *exact* command to execute.

* Shell syntax specifies a command to be wrapped within `/bin/sh -c "..."`.

---

## When to use exec syntax and shell syntax

* shell syntax:

  * is easier to write
  * interpolates environment variables and other shell expressions
  * creates an extra process (`/bin/sh -c ...`) to parse the string
  * requires `/bin/sh` to exist in the container

* exec syntax:

  * is harder to write (and read!)
  * passes all arguments without extra processing
  * doesn't create an extra process
  * doesn't require `/bin/sh` to exist in the container

---

## Pro-tip: the `exec` shell built-in

POSIX shells have a built-in command named `exec`.

`exec` should be followed by a program and its arguments.

From a user perspective:

- it looks like the shell exits right away after the command execution,

- in fact, the shell exits just *before* command execution;

- or rather, the shell gets *replaced* by the command.

---

## Example using `exec`

```dockerfile
CMD exec figlet -f script hello
```

In this example, `sh -c` will still be used, but
`figlet` will be PID 1 in the container.

The shell gets replaced by `figlet` when `figlet` starts execution.

This allows to run processes as PID 1 without using JSON.

---


class: title

## `CMD` and `ENTRYPOINT`

![Container entry doors](images/entrypoint.jpg)

---

## Objectives

In this lesson, we will learn about two important
Dockerfile commands:

`CMD` and `ENTRYPOINT`.

These commands allow us to set the default command
to run in a container.

---

## Defining a default command

When people run our container, we want to greet them with a nice hello message, and using a custom font.

For that, we will execute:

```bash
figlet -f script hello
```

* `-f script` tells figlet to use a fancy font.

* `hello` is the message that we want it to display.

---

## Adding `CMD` to our Dockerfile

Our new Dockerfile will look like this:

```dockerfile
FROM ubuntu
RUN apt-get update
RUN ["apt-get", "install", "figlet"]
CMD figlet -f script hello
```

* `CMD` defines a default command to run when none is given.

* It can appear at any point in the file.

* Each `CMD` will replace and override the previous one.

* As a result, while you can have multiple `CMD` lines, it is useless.

---

## Build and test our image

Let's build it:

```bash
$ docker build -t figlet .
...
Successfully built 042dff3b4a8d
Successfully tagged figlet:latest
```

And run it:

```bash
$ docker run figlet
 _          _   _       
| |        | | | |      
| |     _  | | | |  __  
|/ \   |/  |/  |/  /  \_
|   |_/|__/|__/|__/\__/ 
```

---

## Overriding `CMD`

If we want to get a shell into our container (instead of running
`figlet`), we just have to specify a different program to run:

```bash
$ docker run -it figlet bash
root@7ac86a641116:/# 
```

* We specified `bash`.

* It replaced the value of `CMD`.

---

## Using `ENTRYPOINT`

We want to be able to specify a different message on the command line,
while retaining `figlet` and some default parameters.

In other words, we would like to be able to do this:

```bash
$ docker run figlet salut
           _            
          | |           
 ,   __,  | |       _|_ 
/ \_/  |  |/  |   |  |  
 \/ \_/|_/|__/ \_/|_/|_/
```


We will use the `ENTRYPOINT` verb in Dockerfile.

---

## Adding `ENTRYPOINT` to our Dockerfile

Our new Dockerfile will look like this:

```dockerfile
FROM ubuntu
RUN apt-get update
RUN ["apt-get", "install", "figlet"]
ENTRYPOINT ["figlet", "-f", "script"]
```

* `ENTRYPOINT` defines a base command (and its parameters) for the container.

* The command line arguments are appended to those parameters.

* Like `CMD`, `ENTRYPOINT` can appear anywhere, and replaces the previous value.

Why did we use JSON syntax for our `ENTRYPOINT`?

---

## Implications of JSON vs string syntax

* When CMD or ENTRYPOINT use string syntax, they get wrapped in `sh -c`.

* To avoid this wrapping, we can use JSON syntax.

What if we used `ENTRYPOINT` with string syntax?

```bash
$ docker run figlet salut
```

This would run the following command in the `figlet` image:

```bash
sh -c "figlet -f script" salut
```

---

## Build and test our image

Let's build it:

```bash
$ docker build -t figlet .
...
Successfully built 36f588918d73
Successfully tagged figlet:latest
```

And run it:

```bash
$ docker run figlet salut
           _            
          | |           
 ,   __,  | |       _|_ 
/ \_/  |  |/  |   |  |  
 \/ \_/|_/|__/ \_/|_/|_/
```

---

## Using `CMD` and `ENTRYPOINT` together

What if we want to define a default message for our container?

Then we will use `ENTRYPOINT` and `CMD` together.

* `ENTRYPOINT` will define the base command for our container.

* `CMD` will define the default parameter(s) for this command.

* They *both* have to use JSON syntax.

---

## `CMD` and `ENTRYPOINT` together

Our new Dockerfile will look like this:

```dockerfile
FROM ubuntu
RUN apt-get update
RUN ["apt-get", "install", "figlet"]
ENTRYPOINT ["figlet", "-f", "script"]
CMD ["hello world"]
```

* `ENTRYPOINT` defines a base command (and its parameters) for the container.

* If we don't specify extra command-line arguments when starting the container,
  the value of `CMD` is appended.

* Otherwise, our extra command-line arguments are used instead of `CMD`.

---

## Build and test our image

Let's build it:

```bash
$ docker build -t myfiglet .
...
Successfully built 6e0b6a048a07
Successfully tagged myfiglet:latest
```

Run it without parameters:

```bash
$ docker run myfiglet
 _          _   _                             _        
| |        | | | |                           | |    |  
| |     _  | | | |  __             __   ,_   | |  __|  
|/ \   |/  |/  |/  /  \_  |  |  |_/  \_/  |  |/  /  |  
|   |_/|__/|__/|__/\__/    \/ \/  \__/    |_/|__/\_/|_/
```

---

## Overriding the image default parameters

Now let's pass extra arguments to the image.

```bash
$ docker run myfiglet hola mundo
 _           _                                               
| |         | |                                      |       
| |     __  | |  __,     _  _  _           _  _    __|   __  
|/ \   /  \_|/  /  |    / |/ |/ |  |   |  / |/ |  /  |  /  \_
|   |_/\__/ |__/\_/|_/    |  |  |_/ \_/|_/  |  |_/\_/|_/\__/ 
```

We overrode `CMD` but still used `ENTRYPOINT`.

---

## Overriding `ENTRYPOINT`

What if we want to run a shell in our container?

We cannot just do `docker run myfiglet bash` because
that would just tell figlet to display the word "bash."

We use the `--entrypoint` parameter:

```bash
$ docker run -it --entrypoint bash myfiglet
root@6027e44e2955:/# 
```
---

class: title

## Copying files during the build

![Monks copying books](images/title-copying-files-during-build.jpg)

---

## Objectives

So far, we have installed things in our container images
by downloading packages.

We can also copy files from the *build context* to the
container that we are building.

Remember: the *build context* is the directory containing
the Dockerfile.

In this chapter, we will learn a new Dockerfile keyword: `COPY`.

---

## Build some C code

We want to build a container that compiles a basic "Hello world" program in C.

Here is the program, `hello.c`:

```bash
int main () {
  puts("Hello, world!");
  return 0;
}
```

Let's create a new directory, and put this file in there.

Then we will write the Dockerfile.

---

## The Dockerfile

On Debian and Ubuntu, the package `build-essential` will get us a compiler.

When installing it, don't forget to specify the `-y` flag, otherwise the build will fail (since the build cannot be interactive).

Then we will use `COPY` to place the source file into the container.

```bash
FROM ubuntu
RUN apt-get update
RUN apt-get install -y build-essential
COPY hello.c /
RUN make hello
CMD /hello
```

Create this Dockerfile.

---

## Testing our C program

* Create `hello.c` and `Dockerfile` in the same directory.

* Run `docker build -t hello .` in this directory.

* Run `docker run hello`, you should see `Hello, world!`.

Success!

---

## `COPY` and the build cache

* Run the build again.

* Now, modify `hello.c` and run the build again.

* Docker can cache steps involving `COPY`.

* Those steps will not be executed again if the files haven't been changed.

---

## Details

* You can `COPY` whole directories recursively.

* Older Dockerfiles also have the `ADD` instruction.
  <br/>It is similar but can automatically extract archives.

* If we really wanted to compile C code in a container, we would:

  * Place it in a different directory, with the `WORKDIR` instruction.

  * Even better, use the `gcc` official image.

---

# Building Docker images with a Dockerfile (Advanced)

We will see how to:

* Reduce the number of layers.

* Leverage the build cache so that builds can be faster.

---

## Reducing the number of layers

* Each line in a `Dockerfile` creates a new layer.

* Build your `Dockerfile` to take advantage of Docker's caching system.

* Combine commands by using `&&` to continue commands and `\` to wrap lines.

Note: it is frequent to build a Dockerfile line by line:

```dockerfile
RUN apt-get install thisthing
RUN apt-get install andthatthing andthatotherone
RUN apt-get install somemorestuff
```

And then refactor it trivially before shipping:

```dockerfile
RUN apt-get install thisthing andthatthing andthatotherone somemorestuff
```

---

## Avoid re-installing dependencies at each build

* Classic Dockerfile problem:

  "each time I change a line of code, all my dependencies are re-installed!"

* Solution: `COPY` dependency lists (`package.json`, `requirements.txt`, etc.)
  by themselves to avoid reinstalling unchanged dependencies every time.

---

## Example "bad" `Dockerfile`

The dependencies are reinstalled every time, because the build system does not know if `requirements.txt` has been updated.

```bash
FROM python
WORKDIR /src
COPY . .
RUN pip install -qr requirements.txt
EXPOSE 5000
CMD ["python", "app.py"]
```

---

## Fixed `Dockerfile`

Adding the dependencies as a separate step means that Docker can cache more efficiently and only install them when `requirements.txt` changes.

```bash
FROM python
COPY requirements.txt /tmp/requirements.txt
RUN pip install -qr /tmp/requirements.txt
WORKDIR /src
COPY . .
EXPOSE 5000
CMD ["python", "app.py"]
```

---

## Be careful with `chown`, `chmod`, `mv`

* Layers cannot store efficiently changes in permissions or ownership.

* Layers cannot represent efficiently when a file is moved either.

* As a result, operations like `chown`, `chown`, `mv` can be expensive.

* For instance, in the Dockerfile snippet below, each `RUN` line
  creates a layer with an entire copy of `some-file`.

  ```dockerfile
  COPY some-file .
  RUN chown www-data:www-data some-file
  RUN chmod 644 some-file
  RUN mv some-file /var/www
  ```

* How can we avoid that?

---

## Put files on the right place

* Instead of using `mv`, directly put files at the right place.

* When extracting archives (tar, zip...), merge operations in a single layer.

  Example:

  ```dockerfile
    ...
    RUN wget http://.../foo.tar.gz \
     && tar -zxf foo.tar.gz \
     && mv foo/fooctl /usr/local/bin \
     && rm -rf foo
  ...
  ```

---

## Use `COPY --chown`

* The Dockerfile instruction `COPY` can take a `--chown` parameter.

  Examples:

  ```dockerfile
  ...
  COPY --chown=1000 some-file .
  COPY --chown=1000:1000 some-file .
  COPY --chown=www-data:www-data some-file .
  ```

* The `--chown` flag can specify a user, or a user:group pair.

* The user and group can be specified as names or numbers.

* When using names, the names must exist in `/etc/passwd` or `/etc/group`.

  *(In the container, not on the host!)*

---

## Set correct permissions locally

* Instead of using `chmod`, set the right file permissions locally.

* When files are copied with `COPY`, permissions are preserved.

---

## Embedding unit tests in the build process

```dockerfile
FROM <baseimage>
RUN <install dependencies>
COPY <code>
RUN <build code>
RUN <install test dependencies>
COPY <test data sets and fixtures>
RUN <unit tests>
FROM <baseimage>
RUN <install dependencies>
COPY <code>
RUN <build code>
CMD, EXPOSE ...
```

* The build fails as soon as an instruction fails
* If `RUN <unit tests>` fails, the build doesn't produce an image
* If it succeeds, it produces a clean image (without test libraries and data)

---

## Dockerfile examples

There are a number of tips, tricks, and techniques that we can use in Dockerfiles.

But sometimes, we have to use different (and even opposed) practices depending on:

- the complexity of our project,

- the programming language or framework that we are using,

- the stage of our project (early MVP vs. super-stable production),

- whether we're building a final image or a base for further images,

- etc.

We are going to show a few examples using very different techniques.

---

## When to optimize an image

When authoring official images, it is a good idea to reduce as much as possible:

- the number of layers,

- the size of the final image.

This is often done at the expense of build time and convenience for the image maintainer;
but when an image is downloaded millions of time, saving even a few seconds of pull time
can be worth it.

.small[
```dockerfile
RUN apt-get update && apt-get install -y libpng12-dev libjpeg-dev && rm -rf /var/lib/apt/lists/* \
	&& docker-php-ext-configure gd --with-png-dir=/usr --with-jpeg-dir=/usr \
	&& docker-php-ext-install gd
...
RUN curl -o wordpress.tar.gz -SL https://wordpress.org/wordpress-${WORDPRESS_UPSTREAM_VERSION}.tar.gz \
	&& echo "$WORDPRESS_SHA1 *wordpress.tar.gz" | sha1sum -c - \
	&& tar -xzf wordpress.tar.gz -C /usr/src/ \
	&& rm wordpress.tar.gz \
	&& chown -R www-data:www-data /usr/src/wordpress
```
]

(Source: [Wordpress official image](https://github.com/docker-library/wordpress/blob/618490d4bdff6c5774b84b717979bfe3d6ba8ad1/apache/Dockerfile))

---

## When to *not* optimize an image

Sometimes, it is better to prioritize *maintainer convenience*.

In particular, if:

- the image changes a lot,

- the image has very few users (e.g. only 1, the maintainer!),

- the image is built and run on the same machine,

- the image is built and run on machines with a very fast link ...

In these cases, just keep things simple!

(Next slide: a Dockerfile that can be used to preview a Jekyll / github pages site.)

---

```dockerfile
FROM debian:sid

RUN apt-get update -q
RUN apt-get install -yq build-essential make
RUN apt-get install -yq zlib1g-dev
RUN apt-get install -yq ruby ruby-dev
RUN apt-get install -yq python-pygments
RUN apt-get install -yq nodejs
RUN apt-get install -yq cmake
RUN gem install --no-rdoc --no-ri github-pages

COPY . /blog
WORKDIR /blog

VOLUME /blog/_site

EXPOSE 4000
CMD ["jekyll", "serve", "--host", "0.0.0.0", "--incremental"]
```

---

## Entrypoints and wrappers

It is very common to define a custom entrypoint.

That entrypoint will generally be a script, performing any combination of:

- pre-flights checks (if a required dependency is not available, display
  a nice error message early instead of an obscure one in a deep log file),

- generation or validation of configuration files,

- dropping privileges (with e.g. `su` or `gosu`, sometimes combined with `chown`),

- and more.

---

## Production image

This Dockerfile builds an image leveraging gunicorn:

```dockerfile
FROM python
RUN pip install flask
RUN pip install gunicorn
RUN pip install redis
COPY . /src
WORKDIR /src
CMD gunicorn --bind 0.0.0.0:5000 --workers 10 counter:app
EXPOSE 5000
```
---

## Reducing image size

* Final image should contain:

  * our program

  * its source code

  * the compiler

* Only the first one is strictly necessary.

* We are going to see how to obtain an image without the superfluous components.

---

## Can't we remove superfluous files with `RUN`?

What happens if we do one of the following commands?

- `RUN rm -rf ...`

- `RUN apt-get remove ...`

- `RUN make clean ...`

--

This adds a layer which removes a bunch of files.

But the previous layers (which added the files) still exist.

---

## Removing files with an extra layer

When downloading an image, all the layers must be downloaded.

| Dockerfile instruction | Layer size | Image size |
| ---------------------- | ---------- | ---------- |
| `FROM ubuntu` | Size of base image | Size of base image |
| `...` | ... | Sum of this layer <br/>+ all previous ones |
| `RUN apt-get install somepackage` | Size of files added <br/>(e.g. a few MB) | Sum of this layer <br/>+ all previous ones |
| `...` | ... | Sum of this layer <br/>+ all previous ones |
| `RUN apt-get remove somepackage` | Almost zero <br/>(just metadata) | Same as previous one |

Therefore, `RUN rm` does not reduce the size of the image or free up disk space.

---

## Removing unnecessary files

Various techniques are available to obtain smaller images:

- collapsing layers,

- adding binaries that are built outside of the Dockerfile,

- squashing the final image,

- multi-stage builds.

Let's review them quickly.

---

## Collapsing layers

You will frequently see Dockerfiles like this:

```dockerfile
FROM ubuntu
RUN apt-get update && apt-get install xxx && ... && apt-get remove xxx && ...
```

Or the (more readable) variant:

```dockerfile
FROM ubuntu
RUN apt-get update \
 && apt-get install xxx \
 && ... \
 && apt-get remove xxx \
 && ...
```

This `RUN` command gives us a single layer.

The files that are added, then removed in the same layer, do not grow the layer size.

---

## Building binaries outside of the Dockerfile

This results in a Dockerfile looking like this:

```dockerfile
FROM ubuntu
COPY xxx /usr/local/bin
```

Of course, this implies that the file `xxx` exists in the build context.

That file has to exist before you can run `docker build`.

---

## Building binaries outside: pros and cons

Pros:

- final image can be very small

Cons:

- requires an extra build tool

---

## Multi-stage builds

Multi-stage builds allow us to have multiple *stages*.

Each stage is a separate image, and can copy files from previous stages.

We're going to see how they work in more detail.

---

## Multi-stage builds

* At any point in our `Dockerfile`, we can add a new `FROM` line.

* This line starts a new stage of our build.

* Each stage can access the files of the previous stages with `COPY --from=...`.

* When a build is tagged (with `docker build -t ...`), the last stage is tagged.

* Previous stages are not discarded: they will be used for caching, and can be referenced.

---

## Multi-stage builds in practice

* Each stage is numbered, starting at `0`

* We can copy a file from a previous stage by indicating its number, e.g.:

  ```dockerfile
  COPY --from=0 /file/from/first/stage /location/in/current/stage
  ```

* We can also name stages, and reference these names:

  ```dockerfile
  FROM golang AS builder
  RUN ...
  FROM alpine
  COPY --from=builder /go/bin/mylittlebinary /usr/local/bin/
  ```

---

## Multi-stage builds for our C program

We will change our Dockerfile to:

* give a nickname to the first stage: `compiler`

* add a second stage using the same `ubuntu` base image

* add the `hello` binary to the second stage

* make sure that `CMD` is in the second stage 

The resulting Dockerfile is on the next slide.

---

## Multi-stage build `Dockerfile`

Here is the final Dockerfile:

```dockerfile
FROM ubuntu AS compiler
RUN apt-get update
RUN apt-get install -y build-essential
COPY hello.c /
RUN make hello
FROM ubuntu
COPY --from=compiler /hello /hello
CMD /hello
```

Let's build it, and check that it works correctly:

```bash
docker build -t hellomultistage .
docker run hellomultistage
```

---

## Comparing single/multi-stage build image sizes

List our images with `docker images`, and check the size of:

- the `ubuntu` base image,

- the single-stage `hello` image,

- the multi-stage `hellomultistage` image.

We can achieve even smaller images if we use smaller base images.

---

## Publishing images to the Docker Hub

We have built our first images.

We can now publish it to the Docker Hub!

---

## Logging into our Docker Hub account

* This can be done from the Docker CLI:
  ```bash
  docker login
  ```

---

## Image tags and registry addresses

* Docker images tags are like Git tags and branches.

* They are like *bookmarks* pointing at a specific image ID.

* Tagging an image doesn't *rename* an image: it adds another tag.

* When pushing an image to a registry, the registry address is in the tag.

  Example: `registry.example.net:5000/image`

* What about Docker Hub images?

--

* `ahmedgabercod/clock` is, in fact, `index.docker.io/ahmedgabercod/clock`

* `ubuntu` is, in fact, `library/ubuntu`, i.e. `index.docker.io/library/ubuntu`

---

## Tagging an image to push it on the Hub

* Let's tag our `figlet` image (or any other to our liking):
  ```bash
  docker tag figlet ahmedgabercod/figlet
  ```

* And push it to the Hub:
  ```bash
  docker push ahmedgabercod/figlet
  ```

* That's it!

--

* Anybody can now `docker run ahmedgabercod/figlet` anywhere.

