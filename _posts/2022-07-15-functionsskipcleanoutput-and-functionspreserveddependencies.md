---
layout: post
title: "_FunctionsSkipCleanOutput and FunctionsPreservedDependencies"
date: 2022-07-15T00:00:00.0000000-07:00
---

It is common to run into '`Could not load file or assembly`' errors when developing 
in-process Azure Functions.

For example, "`Message: Could not load file or assembly 'Microsoft.IdentityModel.Protocols, ...`"
or "`Message: Could not load file or assembly 'Microsoft.IdentityModel.Protocols.OpenIdConnect, ...`".

Here's a full example of the error logged:
```
[2021-07-18T19:40:20.200Z] Authorization Failed. System.IO.FileNotFoundException caught while validating JWT token.Message: Could not load file or assembly 'System.IdentityModel.Tokens.Jwt, Version=6.7.1.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35'. The system cannot find the file specified.
```

Both the name and version of the assembly in the error message are important to understanding what's gone wrong.

## The Problem
These errors are typically caused by the in-process Azure Functions runtime when it tries to remove what it believes are redundant assemblies. The runtime includes a bunch of assemblies that it uses. By default it will remove assemblies from the function app that have the same name as assemblies it already has. The runtime is doing that to help improve performance and reduce space.

That behavior doesn't cause any problem when the assemblies being removed are the same versions as those used by the runtime. But that behavior becomes a problem for the function app developer when version is different that what the runtime is using.

Note that these problems don't occur in isolated process (out-of-process) Azure Functions because the runtime runs in a separate process than the function app and doesn't muck with its assemblies.

## Fixes

There are two mechanisms that can be used to fix this problem without playing games with the version of the assemblies or NuGet packages that the function app depends on. Both of these require edits to the function app's `.csproj` file.

1. Skip Cleanout (`_FunctionsSkipCleanOutput`)
2. Preserve Dependencies (`FunctionsPreservedDependencies`)

### Skip Cleanout (`_FunctionsSkipCleanOutput`)

Add `<_FunctionsSkipCleanOutput>true</_FunctionsSkipCleanOutput>` to a `<PropertyGroup>` in the function
app's `.csproj` file. The runtime will now skip removing redundant assemblies das part of its clean out process.

Example:
```xml
<PropertyGroup>
  <_FunctionsSkipCleanOutput>true</_FunctionsSkipCleanOutput>
</PropertyGroup>
```

### Preserve Dependencies (`FunctionsPreservedDependencies`)

Add one or more `FunctionsPreservedDependencies` elements to an `<ItemGroup>` in the function app's `.csproj` file. That tells the runtime not to remove the specified assembly as part of its clean out process.

You'll need to add one `<FunctionsPreservedDependencies>` element for each assembly that the runtime would otherwise be removed by the runtime. That includes assemblies that are indirectly included by your project. So assemblies used by the assemblies your function app uses.

Example:
```xml
<ItemGroup>
  <FunctionsPreservedDependencies Include="Microsoft.IdentityModel.JsonWebTokens.dll" />
  <FunctionsPreservedDependencies Include="Microsoft.IdentityModel.Logging.dll" />
  <FunctionsPreservedDependencies Include="Microsoft.IdentityModel.Protocols.dll" />
  <FunctionsPreservedDependencies Include="Microsoft.IdentityModel.Protocols.OpenIdConnect.dll" />
  <FunctionsPreservedDependencies Include="Microsoft.IdentityModel.Tokens.dll" />
  <FunctionsPreservedDependencies Include="System.IdentityModel.Tokens.Jwt.dll" />
</ItemGroup>
```

Hunting down the assembly names to list in `FunctionsPreservedDependencies` elements
can be time consuming. You'll need to rebuild and trigger each of functions in the function app to see if you've found all of them.

That list will need to be updated as the assemblies or the version of the assemblies used by the function app or the runtime change.

There is a list of assemblies that the in-process Azure Functions runtime uses. That list is maintained at:
https://github.com/Azure/azure-functions-host/blob/dev/tools/ExtensionsMetadataGenerator/test/ExtensionsMetadataGeneratorTests/ExistingRuntimeAssemblies.txt

## Which fix to use?

A dev/devops team might decide between skip cleanout and preserving specific assemblies base on the amount of efficiency they desired in the runtime environments for their function app, and on their capacity to handle additional maintenance.

`_FunctionsSkipCleanOutput` - Use skip cleanout if efficiency-loss due to multiple assemblies being deployed is not a concern. Skipping cleanout doesn't require additional potential maintenance over time.

`FunctionsPreservedDependencies` - Use preserve dependencies if efficiency is important, and worth the cost of the updating the `FunctionsPreservedDependencies` elements as the function app or runtime evolve.

# Documentation (none)

As of the writing of this post there is no official documentation for `_FunctionsSkipCleanOutput` or `FunctionsPreservedDependencies`.

There was some info for `FunctionsPreservedDependencies` [here](https://docs.microsoft.com/en-us/azure/azure-functions/functions-develop-vs#configure-your-build-output-settings) but the "Configure your build output settings" section, where it was mentioned, has been removed. Sad.

There was [an attempt to add some documentation](https://github.com/MicrosoftDocs/azure-docs/pull/77460) but it was shot down.


## References

[https://github.com/bryanknox/AzureFunctionsOpenIDConnectAuthSample/issues/27](https://github.com/bryanknox/AzureFunctionsOpenIDConnectAuthSample/issues/27)

[https://github.com/Azure/azure-functions-vs-build-sdk/issues/397](https://github.com/Azure/azure-functions-vs-build-sdk/issues/397)

[https://github.com/Azure/azure-functions-host/issues/7061](https://github.com/Azure/azure-functions-host/issues/7061)


[https://github.com/Azure/azure-functions-host/blob/dev/tools/ExtensionsMetadataGenerator/test/ExtensionsMetadataGeneratorTests/ExistingRuntimeAssemblies.txt](https://github.com/Azure/azure-functions-host/blob/dev/tools/ExtensionsMetadataGenerator/test/ExtensionsMetadataGeneratorTests/ExistingRuntimeAssemblies.txt)

[https://github.com/MicrosoftDocs/azure-docs/pull/77460](https://github.com/MicrosoftDocs/azure-docs/pull/77460)
