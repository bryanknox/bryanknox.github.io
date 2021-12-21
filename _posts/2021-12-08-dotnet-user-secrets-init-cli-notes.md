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

- [dotnet user-secrets set CLI Notes]({%post_url 2021-12-09-dotnet-user-secrets-set-cli-notes %})

## Table of Contents

<!-- Start Document Outline -->

* [dotnet user-secrets init](#dotnet-user-secrets-init)
	* [Applicable Versions](#applicable-versions)
* [Concepts](#concepts)
* [Synopsis](#synopsis)
* [Description](#description)
	* [Configuration-specific User Secrets](#configuration-specific-user-secrets)
* [Options](#options)
	* [Help Option](#help-option)
	* [Configuration Option](#configuration-option)
		* [Manually adding Configuration-specific UserSecretsIds](#manually-adding-configuration-specific-usersecretsids)
		* [My configuration option wish](#my-configuration-option-wish)
	* [Id Option](#id-option)
	* [Project Option](#project-option)
	* [Verbose Option](#verbose-option)
* [Examples](#examples)
	* [Initialize using defaults](#initialize-using-defaults)
	* [Specify Visual Studio project to initialize](#specify-visual-studio-project-to-initialize)
	* [Specify custom user secrets ID](#specify-custom-user-secrets-id)
	* [Visual Studio projects with PropertyGroup Condition attributes](#visual-studio-projects-with-propertygroup-condition-attributes)
* [See also](#see-also)
	* [Other Documentation](#other-documentation)
* [Acknowledgments](#acknowledgments)

<!-- End Document Outline -->

## dotnet user-secrets init

The `dotnet user-secrets init` command is used to initialize or update a Visual Studio project file so that the project can use secrets stored in a user secrets store.

The command is part of ASP.NET Core's .NET CLI [Secret Manager tool](https://docs.microsoft.com/en-us/aspnet/core/security/app-secrets#secret-manager) tool.

### Applicable Versions
This article applies to: .NET 6.x SDK and later versions.

The notes in this post are based on my observations and experiments using `dotnet user-secrets` CLI tool version: `6.0.0-rtm.21526.8+ae1a6cbe225b99c0bf38b7e31bf60cb653b73a52`

## Concepts

See the **Concepts** section of [dotnet user-secrets CLI Notes]({%post_url 2021-12-07-dotnet-user-secrets-cli-notes %}).

## Synopsis

General synopsis:
```text
Usage: dotnet user-secrets init [options]

Options:
  -?|-h|--help                        Show help information.
  -v|--verbose                        Ignored.
  -p|--project <PROJECT>              Path to project. Defaults to searching the current directory.
  -c|--configuration <CONFIGURATION>  Ignored.
  --id <USERSECRETSID>                The user secrets ID to use.
```

Synopsis for various options: 
```text
dotnet user-secrets init
    [--id <USERSECRETSID>]
    [-p|--project <PROJECT>]

dotnet user-secrets init -?|-h|--help
```

## Description

The `dotnet user-secrets init` command is used to initialize or update a Visual Studio project file so that the project can use secrets stored in a user secrets store.

*The `init` command does NOT create a user secrets store.*

The command *adds* or *updates* the Visual Studio project file's *global* `UserSecretsId`.

The global `UserSecretsId` is the `<UserSecretsId>` element in the first `<PropertyGroup>` element in the Visual Studio project file that does not have a `Condition` attribute.

If no `<PropertyGroup>` element exists in the project file, or if all `<PropertyGroup>` elements have `Condition` attributes, then a `<PropertyGroup>` element (with no `Condition` attribute) is added to the project file, and the  `<UserSecretsId>` element is added to it.

The `<UserSecretsId>` element's inner text is initialized to new GUID by default, unless a `UserSecretsId` is specified by the `--id <USERSECRETSID>` option.

If the [Project Option](#project-option) is not specified then the tool searches the current directory for the Visual Studio project file.

### Configuration-specific User Secrets

The global `UserSecretsId` added or updated in the project by the tool is independent of any configuration associated with the project. It will be used by any configuration that does not have an associated `UserSecretsId` setup in the project file.

If you want to use configuration-specific user secrets stores, then you'll need to manually add `<UserSecretsId>` elements to the `<PropertyGroup>` elements that have `Condition` attributes the associate the `<PropertyGroup>` with the configuration.

See the [Configuration Option](#configuration-option) section below for more info.

## Options

### Help Option

`-?|-h|--help`

Show help information for `dotnet user-secrets init` command.

### Configuration Option

`-c|--configuration <CONFIGURATION>`

Ignored. Currently (in version `6.0.0-rtm.21526.8+ae1a6cbe225b99c0bf38b7e31bf60cb653b73a52`) the `-c|--configuration <CONFIGURATION>` options seems to be ignored and have no effect on the `dotnet user-secrets init` command. *Or, it could be that I haven't figured out how to properly use the `--configuration` option with the `dotnet user-secrets init` command.* 

Other `dotnet user-secrets` commands do support the configuration option.

#### Manually adding Configuration-specific UserSecretsIds

If you want to use configuration-specific user secrets stores, then you'll need to manually edit the Visual Studio project file and add `<UserSecretsId>` elements to the `<PropertyGroup>` elements with `Condition` attributes that associate the `<PropertyGroup>` with the configuration.

```xml
  <PropertyGroup>
    <OutputType>Exe</OutputType>
    <TargetFramework>net5.0</TargetFramework>
    <!-- Global UserSecretsId -->
    <UserSecretsId>myGlobal-UserSecretsId</UserSecretsId>
  </PropertyGroup>

  <PropertyGroup Condition="'$(Configuration)'=='TestConfig1'">
    <!-- TestConfig1 configuration's  UserSecretsId -->
    <UserSecretsId>myTestConfig1-UserSecretsId</UserSecretsId>
  </PropertyGroup> 

  <PropertyGroup Condition="'$(Configuration)'=='TestConfig2'">
    <!-- TestConfig2 configuration's  UserSecretsId -->
    <UserSecretsId>myNewUserSecretId</UserSecretsId>
  </PropertyGroup>
```

#### My configuration option wish

I would like to see the `--configuration` options be supported by the `dotnet user-secrets init` command so that it could be used to initialize or update the global `UserSecretsId` or a configuration-specific `UserSecretsId`.

The following is a sample Visual Studio project file that has a global `UserSecretsId`, plus configuration-specific user secrets IDs for configurations named `TestConfig1` and `TestConfig2`.

```xml
  <PropertyGroup>
    <OutputType>Exe</OutputType>
    <TargetFramework>net5.0</TargetFramework>
    <!-- Global UserSecretsId -->
    <UserSecretsId>myGlobal-UserSecretsId</UserSecretsId>
  </PropertyGroup>

  <PropertyGroup Condition="'$(Configuration)'=='TestConfig1'">
    <!-- TestConfig1 configuration's  UserSecretsId -->
    <UserSecretsId>myTestConfig1-UserSecretsId</UserSecretsId>
  </PropertyGroup> 

  <PropertyGroup Condition="'$(Configuration)'=='TestConfig2'">
    <!-- TestConfig2 configuration's  UserSecretsId -->
    <UserSecretsId>myTestConfig2-UserSecretsId</UserSecretsId>
  </PropertyGroup>
```

##### Example - Update UserSecretsId for Specified Configuration

The following command would update the sample project above by setting the the user secrets ID in the `<UserSecretsId>` element within the `<PropertyGroup>` element where Configuration is `TestConfig2`.

```text
dotnet user-secrets init -c TestConfig1 -id myNewUserSecretId
```

### Id Option

`--id \<USERSECRETSID>`

Specifies the `UserSecretsId` to be set in the Visual Studio project file.

If the Id option is not specified then the tool defaults to using a new GUID as the `UserSecretsId`.

The specified `UserSecretsId` is set as the inner text of the `<UserSecretsId>` element added or updated in the Visual Studio project file.

The `UserSecretsId` to used can be simple text, it does not need to be a GUID.

The user secrets store associated with the specified `UserSecretsId` does not need to already exist.

*The `dotnet user-secrets init` command does NOT create the user secrets store associated with the `UserSecretsId`.*

Currently (version `6.0.0-rtm.21526.8+ae1a6cbe225b99c0bf38b7e31bf60cb653b73a52`) the `UserSecretsId` is used in the file path of the user secrets store's `secret.json` file.

> **Warning:** The `UserSecretsId` should *contain only valid file path characters* for the operating systems of the machines where the Visual Studio project file will be used.
>
> The `dotnet user-secrets init` command does not enforce that requirement and will succeed in setting a `UserSecretsId`, but other `dotnet user-secrets` commands will produce errors like the following example when they encounter such `UserSecretsId`s.
> ```text
> Command failed : Invalid character ':' found in the `UserSecretsId` at index '3'.
> ```
>The limitation is required because in the current Secret Manager tool (version `6.0.0-rtm.21526.8+ae1a6cbe225b99c0bf38b7e31bf60cb653b73a52`) implementation the `UserSecretsId` is used to form the file path to the user secrets store's `secret.json` file.


### Project Option

`-p|--project <PROJECT>`

Path to the Visual Studio project file where the `UserSecretsId` will be added or updated.

If the project option is not specified then the tool defaults to searching the current directory for the Visual Studio project file.

### Verbose Option

`-v|--verbose`

Ignored. As of version `6.0.0-rtm.21526.8+ae1a6cbe225b99c0bf38b7e31bf60cb653b73a52`, the `-v|--verbose` options seems to be ignored and have no effect on the output of the `dotnet user-secrets init` command.

## Examples

### Initialize using defaults

```text
dotnet user-secrets init
```
Generates a new GUID will be used as the `UserSecretsId`.

```xml
<PropertyGroup>
  <TargetFramework>net6.0</TargetFramework>
  <UserSecretsId>03c27eab-1b9f-40b3-b8a0-2c68b57fe526</UserSecretsId>
</PropertyGroup>
```

If the Visual Studio project file already has a `UserSecretsId`, it will not be updated.

### Specify Visual Studio project to initialize

Use the `-p|--project <PROJECT>` option to specify the path to the Visual Studio project files that the `UserSecretsId` should be added to.

```text
dotnet user-secrets init -p D:/repos/my-repos/sample/MyProject/MyProject.cspoj`
```

### Specify custom user secrets ID

Use the `--id <USERSECRETSID>` option to specify the `UserSecretsId` to add or update in the Visual Studio project file.

```text
dotnet user-secrets init `--id local-dev-test1`
```
The following Visual Studio project snippet shows an example of the `<UserSecretsId>` element that is added or updated. Where `local-dev-test1` is the `UserSecretsId` that was specified.

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

In the Visual Studio project file, a `<UserSecretsId>` element is added or updated in the first `<PropertyGroup>` element that does not have a `Condition` attribute. The `<UserSecretsId>` element's inner text is set to `my-user-secret-id`.

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

- [dotnet user-secrets set CLI Notes]({%post_url 2021-12-09-dotnet-user-secrets-set-cli-notes %})

### Other Documentation

As of this posting the best/only documentation I could find is the following in Microsoft Docs:
- [Secret Manager](https://docs.microsoft.com/en-us/aspnet/core/security/app-secrets?view=aspnetcore-6.0&tabs=windows#secret-manager) section of [Safe storage of app secrets in development in ASP.NET Core](https://docs.microsoft.com/en-us/aspnet/core/security/app-secrets?view=aspnetcore-6.0&tabs=windows)

## Acknowledgments

- Special thanks to [Panagiotis Kanavos](https://stackoverflow.com/users/134204/panagiotis-kanavos) whose [answer on Stack Overflow](https://stackoverflow.com/a/60438095/575113) provided the information I needed to understand the `--configuration` option behaves for most of the `dotnet user-secrets` commands.
