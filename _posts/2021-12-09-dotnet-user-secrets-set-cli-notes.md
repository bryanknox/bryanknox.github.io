---
layout: post
title: "dotnet user-secrets set CLI Notes"
date: 2021-12-09T00:00:00.0000000-08:00
---

In this post I try to provide some helpful information that is not currently included in the [official documentation](https://docs.microsoft.com/en-us/aspnet/core/security/app-secrets#secret-manager) for the ASP.NET Core's .NET CLI Secret Manager tool's `dotnet user-secrets set` command.

### Series: ASP.NET Core Secret Manager tool

This post is part of a series about the ASP.NET Core Secret Manager tool that includes:

- [dotnet user-secrets CLI Notes]({%post_url 2021-12-07-dotnet-user-secrets-cli-notes %})

- [dotnet user-secrets init CLI Notes]({%post_url 2021-12-08-dotnet-user-secrets-init-cli-notes %})

- [dotnet user-secrets set CLI Notes]({%post_url 2021-12-09-dotnet-user-secrets-set-cli-notes %}) (this post)

## Table of Contents

<!-- Start Document Outline -->

* [dotnet user-secrets set](#dotnet-user-secrets-set)
	* [Applicable Versions](#applicable-versions)
* [Concepts](#concepts)
* [Synopsis](#synopsis)
* [Description](#description)
* [Arguments](#arguments)
* [Options](#options)
	* [Help Option](#help-option)
	* [Configuration Option](#configuration-option)
	* [Id Option](#id-option)
	* [Project Option](#project-option)
	* [Verbose Option](#verbose-option)
* [Examples](#examples)
	* [Default project and configuration](#default-project-and-configuration)
	* [Piped Input](#piped-input)
* [See also](#see-also)
	* [Other Documentation](#other-documentation)
* [Acknowledgments](#acknowledgments)

<!-- End Document Outline -->

## dotnet user-secrets set

`dotnet user-secrets set` 

The `dotnet user-secrets set` command is used set the value of a named secret in a user secrets store.

The `dotnet user-secrets set` command is part of ASP.NET Core's .NET CLI [Secret Manager tool](https://docs.microsoft.com/en-us/aspnet/core/security/app-secrets#secret-manager) tool.

### Applicable Versions
This article applies to: .NET 6.x SDK and later versions.

The notes in this post are based on my observations and experiments using `dotnet user-secrets` CLI tool version: `6.0.0-rtm.21526.8+ae1a6cbe225b99c0bf38b7e31bf60cb653b73a52`

## Concepts

See the **Concepts** section of [dotnet user-secrets CLI Notes]({%post_url 2021-12-07-dotnet-user-secrets-cli-notes %}).

## Synopsis

General synopsis:
```text
Usage: dotnet user-secrets set [arguments] [options]

Arguments:
  [name]   Name of the secret
  [value]  Value of the secret

Options:
  -?|-h|--help                        Show help information
  -v|--verbose                        Show verbose output
  -p|--project <PROJECT>              Path to project. Defaults to searching the current directory.
  -c|--configuration <CONFIGURATION>  The project configuration to use. Defaults to 'Debug'.
  --id <USERSECRETSID>                The user secrets ID to use.
```

Synopsis for various options: 
```text
dotnet user-secrets set [name] [value]
    [-p|--project <PROJECT>]
    [-c|--configuration <CONFIGURATION>]

dotnet user-secrets set [name] [value]
    --id <USERSECRETSID>

dotnet user-secrets set -?|-h|--help
```

## Description

The `dotnet user-secrets set` command is used set the value of a named secret in a user secrets store.

The user secrets store to be operated on can be specified by 



## Arguments
  
`[name] [value]`

- `[name]`  - Name of the secret to set.
- `[value]` - Value to set for the named secret.
  
## Options

### Help Option

`-?|-h|--help`

Show help information for `dotnet user-secrets set` command.

### Configuration Option

`-c|--configuration <CONFIGURATION>`

Specifies the configuration in the Visual Studio project file that the `UserSecretsId` to use should be associated with.

Defaults to the configuration named "Debug".

Ignored if any id option is specified.

The Visual Studio project file's global `UserSecretsId` will be used if the specified configuration is not associated with a `UserSecretsId` in the project file. Or, an error will be output if project file does not have a global `UserSecretsId`.

### Id Option

`--id <USERSECRETSID>`

Specifies the `UserSecretsId` of the user secrets store to operate on.

If an id option is specified, then the tool will ignore any project option, configuration option, and it will not look for a Visual Studio project file.

### Project Option

`-p|--project <PROJECT>`

Specifies the path to the Visual Studio project file on the local machine that should be used to determine the user secrets store to operate on.

Defaults to searching the current directory on the local machine for the Visual Studio project.

Ignored if any id option is specified.

### Verbose Option

`-v|--verbose`

Show verbose information in the command's output.

The verbose information includes:
- Project file path.
- Secrets file path.
- Additional error and diagnostic details for MSBuild related errors.


## Examples

### Default project and configuration

```text
dotnet user-secrets set ConnectionString "User ID=bob;Password=***"
```

Sets the `ConnectionString` secret to the value `User ID=bob;Password=***`. 

Because no project option was specified, the command looks in the Visual Studio project file in the current directory on the local machine.

No configuration option is specified, so the command looks in the project file for the configuration named "Debug" and uses the `UserSecretsId` associated with that configuration. If no `UserSecretsId` is associated with the Debug configuration in the project file, then the `UserSecretsId` in the project file that is NOT associated with any particular configuration is used. That is referred to as the project's global `UserSecretsId`. If global `UserSecretsId` cannot be found then an error like the following is output.

```text
Could not find the global property 'UserSecretsId' in MSBuild project 'D:\Tests\MyProject\MyProject.csproj'. Ensure this property is set in the project or use the '--id' command line option.
```

### Piped Input

The command will accept piped input. Piped input is expected to be in valid JSON format.

```text
type .\my-secrets.json | dotnet user-secrets set
```

## See also

- [dotnet user-secrets CLI Notes]({%post_url 2021-12-07-dotnet-user-secrets-cli-notes %})

- [dotnet user-secrets init CLI Notes]({%post_url 2021-12-08-dotnet-user-secrets-init-cli-notes %})

### Other Documentation

As of this posting the best/only documentation I could find is the following in Microsoft Docs:
- [Secret Manager](https://docs.microsoft.com/en-us/aspnet/core/security/app-secrets?view=aspnetcore-6.0&tabs=windows#secret-manager) section of [Safe storage of app secrets in development in ASP.NET Core](https://docs.microsoft.com/en-us/aspnet/core/security/app-secrets?view=aspnetcore-6.0&tabs=windows)

## Acknowledgments

- Special thanks to [Panagiotis Kanavos](https://stackoverflow.com/users/134204/panagiotis-kanavos) whose [answer on Stack Overflow](https://stackoverflow.com/a/60438095/575113) provided the information I needed to understand the `--configuration` option behaves for most of the `dotnet user-secrets` commands.
