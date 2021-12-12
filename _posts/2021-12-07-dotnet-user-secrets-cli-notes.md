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
	* [Secrets, user secrets stores and user secrets IDs](#secrets-user-secret-stores-and-user-secrets-ids)
	* [User secrets stores and user secrets IDs](#user-secret-stores-and-user-secrets-ids)
	* [Visual Studio project files, user secrets IDs and configurations](#visual-studio-project-files-user-secrets-ids-and-configurations)
* [Synopsis](#synopsis)
* [Description](#description)
* [Options](#options)
* [Commands](#commands)
	* [Commands for managing user secrets IDs in a Visual Studio project](#commands-for-managing-user-secret-ids-in-a-visual-studio-project)
	* [Commands for managing secrets in a user secrets store](#commands-for-managing-secrets-in-a-user-secrets-store)
* [Examples](#examples)
	* [Show Help](#show-help)
	* [Show Version](#show-version)
* [Implementation Details](#implementation-details)
	* [secret.json user secrets store](#secretjson-user-secret-store)
* [See also](#see-also)
	* [Other Documentation](#other-documentation)
	* [Source Code for dotnet-user-secrets Tool](#source-code-for-dotnet-user-secrets-tool)
* [Acknowledgments](#acknowledgments)

<!-- End Document Outline -->

## dotnet user-secrets

`dotnet user-secrets` is a command line tool for managing the set of secrets in a user secrets store, and managing the user secrets store used by a Visual Studio project.

The `dotnet user-secrets` tool (a.k.a. [Secret Manager tool](https://docs.microsoft.com/en-us/aspnet/core/security/app-secrets#secret-manager)) is a .NET CLI tool that is part of ASP.NET Core.


### Applicable Versions
This article applies to: .NET 6.x SDK and later versions.

The notes in this post are based on my observations and experiments using `dotnet user-secrets` CLI tool version: `6.0.0-rtm.21526.8+ae1a6cbe225b99c0bf38b7e31bf60cb653b73a52`

### Secret Manager vs Secrets Manager

The [Microsoft docs](https://docs.microsoft.com/en-us/aspnet/core/security/app-secrets#secret-manager) call it "Secret Manager", while `dotnet user-secrets --help` calls it "Secrets Manager".

## Concepts

### Secrets and user secrets stores

- A **secret** has a name and a value.

- Sets of secrets are stored in a **user secrets store**.
  
  - The implementation and location of a user secrets store are hidden behind the abstractions provided by the Secret Manager tool. That allows the Secret Manager to be used by developer and code without exposing the implementation details. It also allows the tool to evolve to support user secrets stores in different locations or with different implementations.
  
    - See the [Implementation Details](#implementation-details) section below.
    
    - In these notes I use the term "user secrets store" instead of "secrets file" to honor the intended abstractions.
  
- Each individual secret has a unique name that is used to identify it within the user secrets store.

### User secrets stores and user secrets IDs

- A **user secrets ID** is used to identify a user secrets store.
  
  - Developers can specify a `UserSecretsId` to the `dotnet user-secrets` tool to manage secrets in the user secrets store associated with that `UserSecretsId`.

### Project files, configurations and user secrets IDs

- A `UserSecretsId` can be added to a Visual Studio project file to associate the project with a specific user secrets store.

- When a Visual Studio project file has a `UserSecretsId` the project's code can use secrets in the associated user secrets store via ASP.NET Core's Secret Manager.

- The `dotnet user-secrets` tool can read the `UserSecretsId` from a specified Visual Studio project file, or the tool can search for a project file from which to read the `UserSecretsId`.
    
  - Developers can use the `dotnet user-secrets` tool to manage secrets in the user secrets store associated with the project, without having to explicitly specify the `UserSecretsId`.

- A Visual Studio project file can have multiple **configurations** and each build of the project uses exactly one of the configurations.

- A configuration in a Visual Studio project can be associated with a `UserSecretsId`. That allows the build of the project for a particular configuration to use secrets from the user secrets store associated with that configuration.

  - Developers can specify a configuration to the the `dotnet user-secrets` tool and it will use it to search the the Visual Studio project file for the `UserSecretsId` that should be used.
  
- The use of configurations is optional.
   
   - Developers can setup a Visual Studio project file so that the same `UserSecretsId` is used regardless of the number of configurations that have been setup for the project.
  
 - Configurations are independent of user secrets stores.
   
   - Each configuration can be setup in the project with a different `UserSecretsId`, so that each configuration uses a different store. Or multiple configurations in the same project can use the same `UserSecretsId`, so that they use the same user secrets store.
  
 - The combination of project and configuration can be used by the `dotnet user-secrets` tool to determine the `UserSecretsId` to use for accessing the associated user secrets store.

   - Projects and configurations are used as shortcuts that can be a convenient way for developers to work with secrets in user secrets stores. Once the project file is setup to associate `UserSecretsId`s to its configurations, then the developers can use the `dotnet user-secrets` tool and indicate a configuration. That allows the developer manage the secrets for a particular project configuration without having to remember the specific `UserSecretsId`.

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
  --id <USERSECRETSID>                The user secrets ID to use.

Commands:
  clear   Deletes all the secrets in a user secrets store.
  init    Initialize or update a Visual Studio projectto use a specified user secrets store.
  list    Lists secrets in a user secrets store. 
  remove  Removes the specified secret from a user secrets store.
  set     Sets a secret to a specified value in a user secrets store.
```

Synopsis for usage without a command: 
```text
dotnet user-secrets -?|-h|--help|-v|-verbose

dotnet user-secrets --version
```

## Description

The `dotnet user-secrets` tool can be used for:

- Managing secrets in a user secrets store.

  - See [Commands for managing secrets in a user secrets store](#commands-for-managing-secrets-in-a-user-secrets-store) below.
  
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

### Commands for managing user secrets IDs in a Visual Studio project

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
  --id                                The user secrets ID to use.

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

## Implementation Details

### secret.json user secrets store

The implementation and location of a user secrets store are hidden behind the abstractions provided by the Secret Manager tool. That allows the Secret Manager to be used by developer and code without exposing the implementation details. It also allows the tool to evolve to support user secrets stores in different locations or with different implementations.

> Developers are warned not to write code that depends on the location, storage or implementation details of user secrets stores as those things could changes in the future.

Currently (version `6.0.0-rtm.21526.8+ae1a6cbe225b99c0bf38b7e31bf60cb653b73a52`) user secrets stores are implemented as JSON files named `secret.json` that are stored in the local machine's user profile folder.

**File system path:**

Linux/macOS: `~/.microsoft/usersecrets/<user_secrets_id>/secrets.json`

Windows: `%APPDATA%\Microsoft\UserSecrets\<user_secrets_id>\secrets.json`

Where `<user_secrets_id>` is the user secrets ID that is used to uniquely identify the user secrets store on the local machine.

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
