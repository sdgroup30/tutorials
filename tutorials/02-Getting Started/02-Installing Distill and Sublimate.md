---
id: installing-distill-and-sublimate
title: Installing Distill and Sublimate
---

# Installing Distill and Sublimate

This section will cover installing Distill and Sublimate through Docker. To complete this section, you must have Docker installed. You can find installation instructions for your OS [here](https://docs.docker.com/get-docker/). For this section, Docker is the only prerequisite. 

**NOTE: It is recommended to run these CLI tools on Linux. If installing on Windows, it is recommended to use Windows Subsystem for Linux (WSL). However, the tools will still run on Windows native.**

## Installing Distill

Distill is a tool that will "score" nodes in a network based on a Trivium model of that network. It uses Nessus scan data to score each node and those scores are then pushed back to Trivium.

From the terminal, run the following commands:

```
$ git clone https://github.com/TripleDotEngineering/distill.git

$ cd distill

$ docker compose
```

This will clone the git repository and then create the Docker container. Distill is now installed and ready to be run.

## Installing Sublimate

Using the output from Distill, Sublimate will find the most probable paths an attacker will take through the network and output reports of that information.

Similar to the above, run the following commands in the terminal:

```
$ git clone https://github.com/TripleDotEngineering/sublimate.git

$ cd sublimate

$ docker compose
```

Sublimate is now installed.