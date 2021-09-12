---
layout: post
title: "EmbeddedBlazorContent - how to include static content from blazor libraries"
date: "2019-07-09"
categories: 
  - "blazor"
  - "c"
  - "programming"
tags: 
  - "embeddedblazorcontent"
---

If you want to include some static resources from the Blazor component library use "[EmbeddedBlazorContent](https://www.nuget.org/packages/EmbeddedBlazorContent/)" tool.

Source: [https://github.com/SamProf/EmbeddedBlazorContent](https://github.com/SamProf/EmbeddedBlazorContent)

#### Create static content in Blazor component library

Create "content" folder in the root of the library project and add CSS and JS files in.

#### Add embeded resource build action on all the files

Open properties on each resource file and select **"Embedded resource"** build action on it.

#### Additional attributes for embedded resource in the project file

Open .CSPROJ project file and edit EmbeddedResource definition to have additional LogicalName definition.

**For CSS files:**

  <LogicalName>blazor:css:%(RecursiveDir)%(Filename)%(Extension)</LogicalName>

**For JS files:**

 <LogicalName>blazor:js:%(RecursiveDir)%(Filename)%(Extension)</LogicalName>

Sample:

  <ItemGroup>
    <EmbeddedResource Include="content\\bisaga\_core.css">
      <LogicalName>blazor:css:%(RecursiveDir)%(Filename)%(Extension)</LogicalName>
      <CopyToPublishDirectory>PreserveNewest</CopyToPublishDirectory>
    </EmbeddedResource>
    <EmbeddedResource Include="content\\bisaga\_core.js">
      <LogicalName>blazor:js:%(RecursiveDir)%(Filename)%(Extension)</LogicalName>
      <CopyToPublishDirectory>PreserveNewest</CopyToPublishDirectory>
    </EmbeddedResource>
  </ItemGroup>

### Add EmbeddedBlazorContent library to blazor server side web project

#### Startup.cs

Add configuration for embeded content library to Configure metod in Startup.cs

\# Startup.cs
app.UseEmbeddedBlazorContent(typeof(Bisaga.Core.Components.BsgComponentBase).Assembly);

#### \_Host.cshtml

Add call to EmbeddedBlazorContent where you wish to include embedded files.

@using EmbeddedBlazorContent

<head>
  ...
    @Html.EmbeddedBlazorContent()
</head>
