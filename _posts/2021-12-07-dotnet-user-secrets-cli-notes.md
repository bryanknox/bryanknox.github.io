---
layout: post
title: "dotnet user-secrets CLI Notes"
date: 2021-12-07T00:00:00.0000000-08:00
---

In this post I try to provide some helpful information that is not currently included in the [official documentation](https://docs.microsoft.com/en-us/aspnet/core/security/app-secrets#secret-manager) for the ASP.NET Core's .NET CLI `dotnet user-secrets` tool (a.k.a. Secret Manager tool).

### Series: ASP.NET Core Secret Manager tool

This post is part of a series about the ASP.NET Core Secret Manager tool that includes:

- [dotnet user-secrets CLI Notes]({%post_url 2021-12-07-dotnet-user-secrets-cli-notes %}) (this post)

- [dotnet user-secrets init CLI Notes]({%post_url 2021-12-08-dotnet-user-secrets-init-cli-notes %})

## Table of Contents

<!-- Start Document Outline -->

* [dotnet user-secrets](#dotnet-user-secrets)
	* [Applicable Versions](#applicable-versions)
	* [Secret Manager vs Secrets Manager](#secret-manager-vs-secrets-manager)
* [Concepts](#concepts)
* [Synopsis](#synopsis)
* [Description](#description)
* [Options](#options)
* [Commands](#commands)
	* [Commands for managing user secret IDs in a Visual Studio project](#commands-for-managing-user-secret-ids-in-a-visual-studio-project)
	* [Commands for managing secrets in a user secrets store](#commands-for-managing-secrets-in-a-user-secrets-store)
* [Examples](#examples)
	* [Show Help](#show-help)
	* [Show Version](#show-version)
* [See also](#see-also)
	* [Other Documentation](#other-documentation)
	* [Source Code for dotnet-user-secrets Tool](#source-code-for-dotnet-user-secrets-tool)
* [Acknowledgments](#acknowledgments)

<!-- End Document Outline -->

## dotnet user-secrets

`dotnet user-secrets` is a command line tool for managing the set of secrets in a user secret store, and managing the user secrets store used by a Visual Studio project.

The `dotnet user-secrets` tool (a.k.a. [Secret Manager tool](https://docs.microsoft.com/en-us/aspnet/core/security/app-secrets#secret-manager)) is a .NET CLI tool that is part of ASP.NET Core.


### Applicable Versions
This article applies to: .NET 6.x SDK and later versions.

The notes in this post are based on my observations and experiments using `dotnet user-secrets` CLI tool version: `6.0.0-rtm.21526.8+ae1a6cbe225b99c0bf38b7e31bf60cb653b73a52`

### Secret Manager vs Secrets Manager

The [Microsoft docs](https://docs.microsoft.com/en-us/aspnet/core/security/app-secrets#secret-manager) call it "Secret Manager", while `dotnet user-secrets --help` calls it "Secrets Manager".

## Concepts

Sets of secrets are stored in a **user secret store**.

A **user secrets ID** is used to identify a user secret store on the machine where the Secret Manager is run.

A user secrets ID can be added to a Visual Studio project file to associate the project with a specific user secret store.

When a Visual Studio project file has a user secrets ID:
- The project's code can use secrets in the associated user secrets store via ASP.NET Core's Secret Manager.
- Developers can use the `dotnet user-secrets` commands to manage secrets in the user secrets store associated with the project, without having to explicitly specify the user secrets ID.

## Synopsis

General synopsis:
```text
Usage: dotnet user-secrets [command] [options]

Options:
  -?|-h|--help                        Show help information
  --version                           Show version information
  -v|--verbose                        Show verbose output
  -p|--project <PROJECT>              Path to project. Defaults to searching the current directory.
  -c|--configuration <CONFIGURATION>  The project configuration to use. Defaults to 'Debug'.
  --id <USERSECRETSID>                The user secret ID to use.

Commands:
  clear   Deletes all the application secrets
  init    Set a user secrets ID to enable secret storage
  list    Lists all the application secrets
  remove  Removes the specified user secret
  set     Sets the user secret to the specified value
```

Synopsis for usage without a command: 
```text
dotnet user-secrets -?|-h|--help|-v|-verbose

dotnet user-secrets --version
```

## Description

The `dotnet user-secrets` tool can be used for:

- Managing secrets in a user secret store.

- Initializing or updating a Visual Studio project file so that the project can use secrets stored a specified user secrets store.

  - See [dotnet user-secrets init CLI Notes]({%post_url 2021-12-08-dotnet-user-secrets-init-cli-notes %})

## Options
The options described below are only those for `dotnet user-secrets` when no additional command is specified. For details about options for specific commands, follow the links in the [Commands](#commands) section below.

- `-?|-h|--help`
  
  Show dotnet user-secrets tool help information.
  
- `--version`
  
  Show dotnet user-secrets tool version information.
  
- `-v|--verbose`

  Show verbose output. When no command is specified the output is the same as for `--help`.

## Commands

For details about specific commands and their options follow the links below.

### Commands for managing user secret IDs in a Visual Studio project

- `init` - Initialize or update a Visual Studio project file so that the project can use secrets stored a specified user secrets store.
   
  - `dotnet user-secrets init [options]`
    
  - See [dotnet user-secrets init CLI Notes]({%post_url 2021-12-08-dotnet-user-secrets-init-cli-notes %})

### Commands for managing secrets in a user secrets store

- `clear` - Deletes all the secrets in a user secrets store.

  - `dotnet user-secrets clear [options]`

- `list` - Lists secrets in a user secrets store.

  - `dotnet user-secrets list [options]``

- `remove` - Removes the specified secret from a user secrets store.
  
  - `dotnet user-secrets remove [arguments] [options]`

- `set` - Sets a secret to a specified value in a user secrets store.

  - `dotnet user-secrets set [arguments] [options]`



## Examples
The following are examples of the `dotnet user-secrets` tool without a specified command.

### Show Help

Show `dotnet user-secrets` tool help.

Command Format:
```text
dotnet user-secrets -?|-h|--help|-v|-verbose
```
All of the options above the produce same output.

Example:
```text
dotnet user-secrets --help
```

Output:
```text
User Secrets Manager 6.0.0-rtm.21526.8+ae1a6cbe225b99c0bf38b7e31bf60cb653b73a52

Usage: dotnet user-secrets [options] [command]

Options:
  -?|-h|--help                        Show help information
  --version                           Show version information
  -v|--verbose                        Show verbose output
  -p|--project <PROJECT>              Path to project. Defaults to searching the current directory.
  -c|--configuration <CONFIGURATION>  The project configuration to use. Defaults to 'Debug'.
  --id                                The user secret ID to use.

Commands:
  clear   Deletes all the application secrets
  init    Set a user secrets ID to enable secret storage
  list    Lists all the application secrets
  remove  Removes the specified user secret
  set     Sets the user secret to the specified value

Use "dotnet user-secrets [command] --help" for more information about a command.
```

### Show Version
Show `dotnet user-secrets` tool version information.

Example:
```text
dotnet user-secrets --version
```
Output:
```text
User Secrets Manager
6.0.0-rtm.21526.8+ae1a6cbe225b99c0bf38b7e31bf60cb653b73a52
```

## See also

- [dotnet user-secrets init CLI Notes]({%post_url 2021-12-08-dotnet-user-secrets-init-cli-notes %})

### Other Documentation

As of this posting the best/only documentation I could find is the following in Microsoft Docs:
- [Secret Manager](https://docs.microsoft.com/en-us/aspnet/core/security/app-secrets?view=aspnetcore-6.0&tabs=windows#secret-manager) section of [Safe storage of app secrets in development in ASP.NET Core](https://docs.microsoft.com/en-us/aspnet/core/security/app-secrets?view=aspnetcore-6.0&tabs=windows)

### Source Code for dotnet-user-secrets Tool

The `dotnet user-secrets` tool (a.k.a. Secret Manager tool) is a .NET CLI tool that is part of ASP.NET Core.

The `dotnet user-secrets` tool source code is in GitHub as part of  [dotnet/aspnetcore](https://github.com/dotnet/aspnetcore) repo, in the [src/Tools/dotnet-user-secrets](https://github.com/dotnet/aspnetcore/tree/main/src/Tools/dotnet-user-secrets) folder.

## Acknowledgments

- Special thanks to [Panagiotis Kanavos](https://stackoverflow.com/users/134204/panagiotis-kanavos) whose [answer on Stack Overflow](https://stackoverflow.com/a/60438095/575113) provided the information I needed to understand the `--configuration` option behaves for most of the `dotnet user-secrets` commands.
