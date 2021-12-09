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
* [Synopsis](#synopsis)
* [Description](#description)
* [Options](#options)
* [Commands](#commands)
* [Examples](#examples)
	* [Show Help](#show-help)
	* [Show Version](#show-version)
* [See also](#see-also)
	* [Other Documentation](#other-documentation)
	* [Source Code for dotnet-user-secrets Tool](#source-code-for-dotnet-user-secrets-tool)
* [Acknowledgments](#acknowledgments)

<!-- End Document Outline -->

## dotnet user-secrets

`dotnet user-secrets` is a command line tool for managing the secrets in user secret stores.

The `dotnet user-secrets` tool (a.k.a. Secret Manager tool) is a .NET CLI tool that is part of ASP.NET Core. The [Secret Manager tool](https://docs.microsoft.com/en-us/aspnet/core/security/app-secrets#secret-manager) provides an excellent means of managing sensitive settings data on a local machine for .NET and ASP.NET Core app development.

##### **Secret Manager vs Secrets Manager**  
The [Microsoft docs](https://docs.microsoft.com/en-us/aspnet/core/security/app-secrets#secret-manager) call it "Secret Manager", while `dotnet user-secrets --help` calls it "Secrets Manager".

#### Applicable Versions
This article applies to: .NET 6.x SDK and later versions.

The notes in this post are based on my observations and experiments using `dotnet user-secrets` CLI tool version: `6.0.0-rtm.21526.8+ae1a6cbe225b99c0bf38b7e31bf60cb653b73a52`


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

The `dotnet user-secrets` tool (a.k.a. Secret Manager tool) is a .NET CLI tool that is part of ASP.NET Core.

`dotnet user-secrets` is a command line tool for:
- Adding or updating user secrets IDs in Visual Studio project files.
  - See [dotnet user-secrets init CLI Notes]({%post_url 2021-12-08-dotnet-user-secrets-init-cli-notes %})
- Managing secrets in user secret stores.


## Options
The options described below are only those for `dotnet user-secrets` when no additional command is specified. For details about options for specific commands, follow the links in the Commands section below.

- `-?|-h|--help`
  
  Show dotnet user-secrets tool help information.
  
- `--version`
  
  Show dotnet user-secrets tool version information.
  
- `-v|--verbose`

  Show verbose output. When no command is specified the output is the same as for `--help`.

## Commands

For details about commands, and their options follow the links for the specific commands below.

Commands:

- `clear` - Deletes all the application secrets.
  
- `init` - Set a user secrets ID to enable secret storage.
   
  - `dotnet user-secrets init [options]`
    
  - See [dotnet user-secrets init CLI Notes]({%post_url 2021-12-08-dotnet-user-secrets-init-cli-notes %})

- `list` - Lists all the application secrets.

- `remove` - Removes the specified user secret.

- `set` - Sets the user secret to the specified value.


## Examples

### Show Help

Show `dotnet user-secrets` help.

Command Format:
```text
dotnet user-secrets -?|-h|--help|-v|-verbose
```
All of the above do the produce same output.

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