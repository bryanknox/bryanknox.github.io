---
layout: post
title: "dotnet user-secrets init CLI Notes"
date: 2021-12-07T00:00:00.0000000-08:00
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
* [Synopsis](#synopsis)
* [Description](#description)
* [Options](#options)
	* [Help Option](#help-option)
	* [Configuration Option](#configuration-option)
		* [My configuration option wish](#my-configuration-option-wish)
	* [Id Option](#id-option)
	* [Project Option](#project-option)
	* [Verbose Option](#verbose-option)
* [Examples](#examples)
	* [Specify Visual Studio project to initialize](#specify-visual-studio-project-to-initialize)
	* [Specify custom user secret ID](#specify-custom-user-secret-id)
	* [Visual Studio projects with PropertyGroup Condition attributes](#visual-studio-projects-with-propertygroup-condition-attributes)
* [See also](#see-also)
	* [Other Documentation](#other-documentation)

<!-- End Document Outline -->

## dotnet user-secrets init

`dotnet user-secrets init` is used to *add* or *update* a *user secrets ID* in a Visual Studio project file so that the ASP.NET Core's Secret Manager can be used for secret storage in development environments.

The `dotnet user-secrets init` command is part of ASP.NET Core's .NET CLI [Secret Manager tool](https://docs.microsoft.com/en-us/aspnet/core/security/app-secrets#secret-manager) tool.

#### Applicable Versions
This article applies to: .NET 6.x SDK and later versions.

The notes in this post are based on my observations and experiments using `dotnet user-secrets` CLI tool version: `6.0.0-rtm.21526.8+ae1a6cbe225b99c0bf38b7e31bf60cb653b73a52`

## Synopsis

General synopsis:
```text
Usage: dotnet user-secrets init [options]

Options:
  -?|-h|--help                        Show help information.
  -v|--verbose                        Show verbose output.
  -p|--project <PROJECT>              Path to project. Defaults to searching the current directory.
  -c|--configuration <CONFIGURATION>  Ignored. The project configuration to use. Defaults to 'Debug'.
  --id <USERSECRETSID>                The user secret ID to use.
```

Synopsis for various options: 
```text
dotnet user-secrets init
    [--id <USERSECRETSID>]
    [-p|--project <PROJECT>]
    [-v|--verbose]

dotnet user-secrets init -?|-h|--help
```

## Description

The `dotnet user-secrets init` command is used to *add* or *update* a *user secrets ID* in a Visual Studio project file.

A Visual Studio project file with a user secrets ID can use secrets storage in development environments via the ASP.NET Core's Secret Manager. And so that other `dotnet user-secrets` commands can then be used with the Visual Studio project without having to specify a user secrets ID.

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

As of version `6.0.0-rtm.21526.8+ae1a6cbe225b99c0bf38b7e31bf60cb653b73a52`, the `-c|--configuration <CONFIGURATION>` option seems to be ignored and has no effect on the `dotnet user-secrets init` command. *Or, it could be that I haven't figured out how to properly use the `--configuration` option with the `dotnet user-secrets init` command.* For other `dotnet user-secrets` commands it is used to specify the project configuration to use.

#### My configuration option wish

I would like to see the `--configuration` options used to specify the configuration in the project file that the user secrets ID should be added or updated for. 

For example:
```text
dotnet user-secrets init -c TestConfig1 -id myNewUserSecretId
```

That command would update the `<UserSecretsId>` element in the `<PropertyGroup>` element where Configuration is `TestConfig2`. As shown in the following project file snippet.

```xml
  <PropertyGroup>
    <OutputType>Exe</OutputType>
    <TargetFramework>net5.0</TargetFramework>
  </PropertyGroup>

  <PropertyGroup Condition="'$(Configuration)'=='TestConfig1'">
    <UserSecretsId>myTestConfig1-UserSecretsId</UserSecretsId>
  </PropertyGroup> 

  <PropertyGroup Condition="'$(Configuration)'=='TestConfig2'">
    <UserSecretsId>myNewUserSecretId</UserSecretsId>
  </PropertyGroup>
```


However, that may be tough to implement given that `Condition` attributes can be complex. For example:
```xml
<PropertyGroup Condition="'$(Configuration)|$(Platform)'=='Debug|AnyCPU'">`
```

### Id Option

`--id \<USERSECRETSID>`

Specifies the user secret ID to be set in the Visual Studio project file.

Defaults to a new GUID.

The user secret ID is set as the value of the `<UserSecretsId>` element added to the Visual Studio project file.

The user secret ID to used can be simple text, it does not need to be a GUID. 

Elsewhere, the user secret ID is part of the user secrets file path, so it should only contain valid file path characters for the operating systems of the environments where it is used.

### Project Option

`-p|--project <PROJECT>`

Path to the Visual Studio project file where the user secrets ID will be added or updated.

Defaults to searching the current directory.

### Verbose Option

`-v --verbose`

Show verbose output.

## Examples

### Specify Visual Studio project to initialize
Use the `-p|--project <PROJECT>` option to specify the path to the Visual Studio project files that the user secret ID should be added to.

```text
dotnet user-secrets init -p D:/repos/my-repos/sample/MyProject/MyProject.cspoj`
```

### Specify custom user secret ID
Use the `--id <USERSECRETSID>` option to specify the user secret ID to add to the Visual Studio project file.

```text
dotnet user-secrets init `--id local-dev-test1`
```
The following Visual Studio project snippet shows an example of the `<UserSecretsId>` element that is added. Where `local-dev-test1` is the user secret ID that was specified.

```xml
<PropertyGroup>
  <TargetFramework>net6.0</TargetFramework>
  <UserSecretsId>local-dev-test1</UserSecretsId>
</PropertyGroup>
```

### Visual Studio projects with PropertyGroup Condition attributes

```text
dotnet user-secrets init --id my-user-secret-id
```

In the Visual Studio project file, a `<UserSecretsId>` element with inner text of `my-user-secret-id` is added to the first `<PropertyGroup>` element that does not have a `Condition` attribute.

```xml
<PropertyGroup Condition="'$(Configuration)'=='Release'">
    <!-- First PropertyGroup with a Condition. -->
</PropertyGroup>
<PropertyGroup Condition="'$(Configuration)'=='Debug'">
    <!-- Second PropertyGroup with a Condition. -->
</PropertyGroup>
<PropertyGroup>
  <!-- First PropertyGroup without a Condition. -->
  <UserSecretsId>my-user-secret-id</UserSecretsId>
</PropertyGroup>
<PropertyGroup>
  <!-- Second PropertyGroup without a Condition. -->
</PropertyGroup>
```

## See also

- [dotnet user-secrets CLI Notes]({%post_url 2021-12-07-dotnet-user-secrets-cli-notes %})

### Other Documentation

As of this posting the best/only documentation I could find is the following in Microsoft Docs:
- [Secret Manager](https://docs.microsoft.com/en-us/aspnet/core/security/app-secrets?view=aspnetcore-6.0&tabs=windows#secret-manager) section of [Safe storage of app secrets in development in ASP.NET Core](https://docs.microsoft.com/en-us/aspnet/core/security/app-secrets?view=aspnetcore-6.0&tabs=windows)
