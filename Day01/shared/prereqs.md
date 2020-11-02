# Pre-requirements

- Be comfortable with the Linux command line

  - navigating directories

  - editing files

  - a little bit of bash-fu (environment variables, loops)

- It's totally OK if you are not a Linux expert!

---

## Hands-on sections

- The whole workshop is hands-on

- We are going to build, ship, and run containers!

- All hands-on sections are clearly identified, like the gray rectangle below

.exercise[

- This is the stuff you're supposed to do!

- Go to @@SLIDES@@ to view these slides

<!-- ```open @@SLIDES@@``` -->

]

---

class: in-person

## Play with Docker: https://labs.play-with-docker.com/

- Each person gets a private cluster of cloud VMs (not shared with anybody else)

- They'll remain up for 4 hours

- You can automatically SSH from one VM to another

- The nodes have aliases: `node1`, `node2`, etc.

---

class: in-person

## Why don't we run containers locally?

- Installing this stuff can be hard on some machines

  (32 bits CPU or OS... Laptops without administrator access... etc.)

- All you need is a computer (or even a phone or tablet!), with:

  - an internet connection

  - a web browser

  - an SSH client

---

class: in-person

## SSH clients

- On Linux, OS X, FreeBSD... you are probably all set

- On Windows, get one of these:

  - [putty](http://www.putty.org/)
  - Microsoft [Win32 OpenSSH](https://github.com/PowerShell/Win32-OpenSSH/wiki/Install-Win32-OpenSSH)
  - [Git BASH](https://git-for-windows.github.io/)
  - [MobaXterm](http://mobaxterm.mobatek.net/)

- Nice-to-have: [Mosh](https://mosh.org/) instead of SSH, if your internet connection tends to lose packets

