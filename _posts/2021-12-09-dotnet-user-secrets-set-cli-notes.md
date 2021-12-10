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

Ignored. As of version `6.0.0-rtm.21526.8+ae1a6cbe225b99c0bf38b7e31bf60cb653b73a52`, the `-c|--configuration <CONFIGURATION>` options seems to be ignored and have no effect on the `dotnet user-secrets init` command. *Or, it could be that I haven't figured out how to properly use the `--configuration` option with the `dotnet user-secrets init` command.* For other `dotnet user-secrets` commands it is used to specify the project configuration to use.

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

The specified user secret ID is set as the inner text of the `<UserSecretsId>` element added or updated in the Visual Studio project file.

The user secret ID to used can be simple text, it does not need to be a GUID. 

Elsewhere, the user secret ID is part of the user secrets file path, so it should only contain valid file path characters for the operating systems of the environments where it is used.

### Project Option

`-p|--project <PROJECT>`

Path to the Visual Studio project file where the user secrets ID will be added or updated.

Defaults to searching the current directory for the Visual Studio project file.

### Verbose Option

`-v|--verbose`

Ignored. As of version `6.0.0-rtm.21526.8+ae1a6cbe225b99c0bf38b7e31bf60cb653b73a52`, the `-v|--verbose` options seems to be ignored and have no effect on the output of the `dotnet user-secrets init` command.

Show verbose output.

## Examples

### Initialize using defaults

```text
dotnet user-secrets init
```
Generates a new GUID will be used as the user secret ID.

```xml
<PropertyGroup>
  <TargetFramework>net6.0</TargetFramework>
  <UserSecretsId>03c27eab-1b9f-40b3-b8a0-2c68b57fe526</UserSecretsId>
</PropertyGroup>
```

If the Visual Studio project file already has a user secret ID, it will not be updated.

### Specify Visual Studio project to initialize

Use the `-p|--project <PROJECT>` option to specify the path to the Visual Studio project files that the user secret ID should be added to.

```text
dotnet user-secrets init -p D:/repos/my-repos/sample/MyProject/MyProject.cspoj`
```

### Specify custom user secret ID

Use the `--id <USERSECRETSID>` option to specify the user secret ID to add or update in the Visual Studio project file.

```text
dotnet user-secrets init `--id local-dev-test1`
```
The following Visual Studio project snippet shows an example of the `<UserSecretsId>` element that is added or updated. Where `local-dev-test1` is the user secret ID that was specified.

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

- [dotnet user-secrets init CLI Notes]({%post_url 2021-12-08-dotnet-user-secrets-init-cli-notes %})

### Other Documentation

As of this posting the best/only documentation I could find is the following in Microsoft Docs:
- [Secret Manager](https://docs.microsoft.com/en-us/aspnet/core/security/app-secrets?view=aspnetcore-6.0&tabs=windows#secret-manager) section of [Safe storage of app secrets in development in ASP.NET Core](https://docs.microsoft.com/en-us/aspnet/core/security/app-secrets?view=aspnetcore-6.0&tabs=windows)

## Acknowledgments

- Special thanks to [Panagiotis Kanavos](https://stackoverflow.com/users/134204/panagiotis-kanavos) whose [answer on Stack Overflow](https://stackoverflow.com/a/60438095/575113) provided the information I needed to understand the `--configuration` option behaves for most of the `dotnet user-secrets` commands.
