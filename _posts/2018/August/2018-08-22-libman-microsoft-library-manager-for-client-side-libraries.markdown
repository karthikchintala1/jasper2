---
layout: "post"
title: "LibMan - Microsoft Library Manager for client side libraries"
date: "2018-08-22 09:52"
navigation: true
layout: post
current: post
class: post-template
subclass: 'post'
author: karthikchintala
tags: ["LibMan", "libman.json", "Visual Studio", "Microsoft"]
cover: 'assets/covers/library.jpg'
description: libman.json is a library manager for .net core applications in the client side
---

Back in the days if we want to download jQuery library for a POC of a project we used to download the required js and CSS files from jquery site and then locating your folder to place your libraries within. Although, this job is being now replaced with package management systems you'll need to install a package management system to do it for us. LibMan does it elegantly without any package manager.

### What is LibMan?
> LibMan(libman.json) is for managing client-side libraries in projects. It is a way of organizing client-side libraries into a specific folder without much hassle.

LibMan is added in Visual Studio as part of the update 15.8. Note that this is an experimental feature built into visual studio.

### Purpose
- With LibMan, you don't need any additional package management system to be installed to download client-side libraries for projects. This doesn't mean that libman is a package management system.
- Reduces the build tasks of organizing the library files.
- You can have the libman for any web projects. LibMan is not tied to any specific project.

### LibMan
For Web Projects, you can just open the folder in Visual Studio and add a `libman.json` by yourself or you can do it with context menu option as well. We'll see how to do it.

In this post, I'll just create a folder as `app` and open this folder as a website and add the libman in here. Notice the empty folders in my solution here.

![empty folders](assets\posts\libman\empty_folders.png)

Now, right click on the app and select `Manage Client-Side Libraries…` from the context menu. This should create a `libman.json` file in the project.
or
Go to `WebSite/Project` and select `Manage Client-Side Libraries…` from the menu

Both of the above options would create `libman.json` file in the project with the default configuration (if the `libman.json` does not exist already)

![default_config](assets\posts\libman\default_config.png)

## Options in LibMan:
- defaultProvider: LibMan offers 3 different providers
    - cdnjs (default provider)
    - filesystem (from local files)
    - unpkg (a fast, global cdn)
- defaultDestination: The default destination to all store the libraries, if the destination for a library is not specified. This option is not included by default in the libman.json config file.
- libraries: An array of libraries to configure.

### Downloading jQuery and storing in website
As part of this article, we'll just download the latest version of the jQuery library from `cdnjs` network and map it to our local `lib` folder on the website.

One of the good things about this LibMan integration into Visual Studio is that it provides an intellisense if the provider is configured to a CDN network such as `cdnjs` or `unpkg`. The libman.json is not just a JSON file but we'll have some intellisense while we configure it.

![library intellisense](assets\posts\libman\library_intellisense.png)

Let's map the jQuery to the solution/project folder.

Add the following into the `libraries` array of `libman.json`
{% highlight json %}
{
      "library": "jquery@3.3.1",
      "destination": "lib/jQuery_3.3.1",
      "provider": "cdnjs"
}
{% endhighlight %}

After you copy and paste the above code. Save the file. Now the jQuery files should be downloaded as you can see in the output window.

![restored](assets\posts\libman\restored.png)

As you can see in the lib folder jQuery library is downloaded.

![jquery download](assets\posts\libman\jquery_downloaded.png)

Notice that only a few js files are downloaded and no `CSS` files are downloaded.

You can customize the files that you want to download using the files options inside a library object
Ex:
{% highlight json %}
{
      "library": "jqueryui@1.12.1",
      "destination": "lib/jQuery_UI",
      "provider": "cdnjs",
      "files": [ "jquery-ui.css", "images/ui-icons_444444_256x240.png" ]
}
{% endhighlight %}

So, you can download whatever the files you want.

### Restoring manually
You can still restore the files manually by right-clicking on the project folder and selecting `Restore Client-Side Libraries`.

![restore manually](assets\posts\libman\restore_manually.png)

### Restoring libraries on Build
There is an option to restore the files during the build as well. This can be done by right-clicking on the `libman.json` and selecting `Enable Restore Client-Side Libraries on Build..`

![restore on build](assets\posts\libman\restore_on_build.png)

This will prompt you for an alert dialog in Visual Studio

![library manager alert](assets\posts\libman\library_manager_alert.png)

This will add a package to the packages in the package.json file as we are in a web project.

{% highlight xml %}
<?xml version="1.0" encoding="utf-8"?>
<packages>
  <package id="Microsoft.Web.LibraryManager.Build" version="1.0.163" targetFramework="net40" />
</packages>
{% endhighlight %}

If you are on a csharp project, then a package reference will be added in the `.csproj` file

{% highlight xml %}
<Project Sdk="Microsoft.NET.Sdk.Web">

  <PropertyGroup>
    <TargetFramework>netcoreapp2.1</TargetFramework>
  </PropertyGroup>

  <ItemGroup>
    <PackageReference Include="Microsoft.AspNetCore.App" />
    <PackageReference Include="Microsoft.Web.LibraryManager.Build" Version="1.0.163" />
  </ItemGroup>

</Project>
{% endhighlight %}

### Takeaway
LibMan is a great addition to the visual studio. LibMan will avoid you to stop downloading the client side libraries from the web and unzip them and add it to the appropriate library folders that we wish to have. A cleaner way to set up the client side libraries without much effort.

If you are a guy who does Research and Development (R&D) all day long this LibMan is for you. You don't need to search for the libraries around the web. Just use LibMan.

This LibMan still has its limitations to few providers as of now. But, hopefully, Microsoft might add few of them over time.

That's it for the post.
