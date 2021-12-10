---
layout: post
title: "dotnet user-secrets init CLI Notes"
date: 2021-12-08T00:00:00.0000000-08:00
---

In this post I try to provide some helpful information that is not currently included in the [official documentation](https://docs.microsoft.com/en-us/aspnet/core/security/app-secrets#secret-manager) for the ASP.NET Core's .NET CLI Secret Manager tool's `dotnet user-secrets init` command.

### Series: ASP.NET Core Secret Manager tool

This post is part of a series about the ASP.NET Core Secret Manager tool that includes:

- [dotnet user-secrets CLI Notes]({%post_url 2021-12-07-dotnet-user-secrets-cli-notes %})

- [dotnet user-secrets init CLI Notes]({%post_url 2021-12-08-dotnet-user-secrets-init-cli-notes %})  (this post)

## Table of Contents

<!-- Start Document Outline -->

* [dotnet user-secrets init](#dotnet-user-secrets-init)
	* [Applicable Versions](#applicable-versions)
* [Concepts](#concepts)
* [Synopsis](#synopsis)
* [Description](#description)
* [Options](#options)
	* [Help Option](#help-option)
	* [Configuration Option](#configuration-option)
	* [Id Option](#id-option)
	* [Project Option](#project-option)
	* [Verbose Option](#verbose-option)
* [Examples](#examples)
* [See also](#see-also)
	* [Other Documentation](#other-documentation)

<!-- End Document Outline -->

## dotnet user-secrets init

The `dotnet user-secrets init` command is used to initialize or update a Visual Studio project file so that the project can use secrets stored a specified user secrets store.

The `dotnet user-secrets init` command is part of ASP.NET Core's .NET CLI [Secret Manager tool](https://docs.microsoft.com/en-us/aspnet/core/security/app-secrets#secret-manager) tool.

### Applicable Versions
This article applies to: .NET 6.x SDK and later versions.

The notes in this post are based on my observations and experiments using `dotnet user-secrets` CLI tool version: `6.0.0-rtm.21526.8+ae1a6cbe225b99c0bf38b7e31bf60cb653b73a52`

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
Usage: dotnet user-secrets init [options]

Options:
  -?|-h|--help                        Show help information.
  -v|--verbose                        Ignored.
  -p|--project <PROJECT>              Path to project. Defaults to searching the current directory.
  -c|--configuration <CONFIGURATION>  Ignored.
  --id <USERSECRETSID>                The user secret ID to use.
```

Synopsis for various options: 
```text
dotnet user-secrets init
    [--id <USERSECRETSID>]
    [-p|--project <PROJECT>]

dotnet user-secrets init -?|-h|--help
```

## Description

The `dotnet user-secrets init` command is used to *add* or *update* a *user secrets ID* in a Visual Studio project file.



> The `dotnet user-secrets init` command does NOT create a secrets file.

Adds or updates a `<UserSecretsId>` element in the first `<PropertyGroup>` element in the Visual Studio project file that does not have a `Condition` attribute. 

If no `<PropertyGroup>` element exists in the project file, or if all `<PropertyGroup>` elements have `Condition` attributes, then a `<PropertyGroup>` element (with no `Condition` attribute) is added to the project file, and the  `<UserSecretsId>` element is added to it.

The `<UserSecretsId>` element's inner text is initialized to new GUID by default, unless a user secrets ID is specified by the `--id <USERSECRETSID>` option.

Searches the current directory for the Visual Studio project file by default, unless the path to a project file is specified by the `-p|--project <PROJECT>` option.


## Options

### Help Option

`-?|-h|--help`

Show help information for `dotnet user-secrets init` command.

### Configuration Option

`-c|--configuration <CONFIGURATION>`

### Id Option

`--id \<USERSECRETSID>`


### Project Option

`-p|--project <PROJECT>`

Path to the Visual Studio project file where the user secrets ID to use can be found.

Defaults to searching the current directory for the Visual Studio project file.

### Verbose Option

`-v|--verbose`

Show verbose output.

## Examples


## See also

- [dotnet user-secrets CLI Notes]({%post_url 2021-12-07-dotnet-user-secrets-cli-notes %})

### Other Documentation

As of this posting the best/only documentation I could find is the following in Microsoft Docs:
- [Secret Manager](https://docs.microsoft.com/en-us/aspnet/core/security/app-secrets?view=aspnetcore-6.0&tabs=windows#secret-manager) section of [Safe storage of app secrets in development in ASP.NET Core](https://docs.microsoft.com/en-us/aspnet/core/security/app-secrets?view=aspnetcore-6.0&tabs=windows)
