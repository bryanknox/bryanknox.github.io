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
  --id                                The user secret ID to use.

Additional Info:
  This command will also handle piped input. Piped input is expected to be a valid JSON format.

Examples:
  dotnet user-secrets set ConnStr "User ID=bob;Password=***"
  type .\secrets.json | dotnet user-secrets set
```

Synopsis for various options: 
```text
```

## Description

The `dotnet user-secrets set` command ...

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

Specifies the configuration of the Visual Studio project that should be used to determine the user secrets store to operate on.

Defaults to the configuration named "Debug".

Ignored if any id option is specified.

### Id Option

`--id \<USERSECRETSID>`

Specifies the user secret ID of the user secrets store to operate on.

If an id option is specified, then the tool will ignore any project option, configuration option, or any user secrets ID in the local Visual Studio project file.

### Project Option

`-p|--project <PROJECT>`

Specifies the path to the Visual Studio project file on the local machine that should be used to determine the user secrets store to operate on.

Defaults to searching the current directory on the local machine for the Visual Studio project.

Ignored if any id option is specified.

### Verbose Option

`-v|--verbose`

Ignored. As of version `6.0.0-rtm.21526.8+ae1a6cbe225b99c0bf38b7e31bf60cb653b73a52`, the `-v|--verbose` options seems to be ignored and have no effect on the output of the `dotnet user-secrets init` command.

Show verbose output.

## Examples


## See also

- [dotnet user-secrets CLI Notes]({%post_url 2021-12-07-dotnet-user-secrets-cli-notes %})

- [dotnet user-secrets init CLI Notes]({%post_url 2021-12-08-dotnet-user-secrets-init-cli-notes %})

### Other Documentation

As of this posting the best/only documentation I could find is the following in Microsoft Docs:
- [Secret Manager](https://docs.microsoft.com/en-us/aspnet/core/security/app-secrets?view=aspnetcore-6.0&tabs=windows#secret-manager) section of [Safe storage of app secrets in development in ASP.NET Core](https://docs.microsoft.com/en-us/aspnet/core/security/app-secrets?view=aspnetcore-6.0&tabs=windows)

## Acknowledgments

- Special thanks to [Panagiotis Kanavos](https://stackoverflow.com/users/134204/panagiotis-kanavos) whose [answer on Stack Overflow](https://stackoverflow.com/a/60438095/575113) provided the information I needed to understand the `--configuration` option behaves for most of the `dotnet user-secrets` commands.
