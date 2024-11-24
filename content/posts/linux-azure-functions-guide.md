+++
date = '2024-11-21T19:32:53Z'
draft = true
title = 'Azure Functions with .NET on Linux'
summary = 'A guide to running a .NET8 Azure Functions project on a Linux App Service Plan'
toc = true
+++

## Overview

The MS documentation for running a .NET Azure Functions project is pretty comprehensive, but scattershot, and it doesn't offer a great deal of help when troubleshooting. Documented below are some of the key pain points for either making the migration, or starting fresh.

## Why Linux?

It might be uninviting to make the move if you're developing in a Windows environment (which most .NET developers are). However, there are a couple of key motivators:

- __Cost__
    - There is a **dramatic** difference in pricing between Windows App Service Plans and Linux. You're likely to save at least 50%, and up to 80%, on your bills.
- __Memory/CPU availability__
    - Detailed a bit further below, but you might find that some resource is freed up on your App Service Plan. This could mean the ability to scale down, to run multiple apps on the same plan, etc.

## ARM/BICEP/Terraform Template Updates

If you're using some kind of IaC to define your infrastructure, you'll need to make some changes to your templates. The migration can't be done seamlessly by the Azure Resource Manager - at minimum, you'll need to delete any App Service/Function resources running on the App Service Plan and redeploy. If you can't abide downtime, you'll need to plan around this.

### App Service Plan

### Site Properties

### App settings/environment variables

Environment variables in Linux cannot contain colons (```:```). If you're templating your app settings/env variables, replace any uses of a colon with a double underscore (```__```). You may run into this if you're using modern configuration builders, and have your app settings organised into sections, such as:

```
"ConfigSectionA": {
    "SettingA": "ValueA",
    "SettingB": "ValueB",
},
```
However awkward, you'll have to represent these in your BICEP template as:

```
{
    name: 'ConfigSectionA__SettingA'
    value: 'ValueA'
}
{
    name: 'ConfigSectionA__SettingB'
    value: 'ValueB'
}
```

## Build Changes - Azure DevOps

