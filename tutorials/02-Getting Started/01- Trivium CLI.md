---
id: trivium-cli
title: Trivium CLI
---

# Trivium CLI

The Trivium CLI is a Python module that allows the user to interact with the Trivium API through the command line. You can both push and pull data to Trivium using the module, and is the main way Distill & Sublimate connect to Trivium. This section will cover installing and configuring the Trivium CLI.

## Installing the CLI

To install the CLI, first ensure you have both `Python` and `pip` installed on your machine. Then, run the following command from the terminal: 

```
$ pip install trivium-cli
```

The Trivium CLI is now installed

## Configuring the CLI

To configure the CLI, run the following command from the terminal:

```
$ trivium configure
```

Then, input your Trivium username and password into the two fields. You are now logged in through the CLI. To ensure you have logged in correctly, run the following command:

```
$ trivium whoami
```

You should receive a JSON response containing your user information.