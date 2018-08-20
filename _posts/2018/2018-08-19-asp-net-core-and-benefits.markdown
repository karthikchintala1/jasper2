---
layout: "post"
title: "ASP.NET Core and Benefits"
date: "2018-08-19 21:03"
navigation: true
layout: post
current: post
class: post-template
subclass: 'post'
author: karthikchintala
tags: [ASP.NET CORE, ASP.NET vNext, Benefits]
cover: 'assets/covers/benefits.jpg'
---

ASP.NET Core is the new web framework for the Microsoft technologies. We'll see the benefits of using the framework.

### How did the name ASP.NET Core come?
> Originally deemed `ASP.NET vNext`, the framework was going to be called `ASP.NET 5` when ready. However, in order to avoid implying it is an update to the existing ASP.NET framework, Microsoft later changed the name to `ASP.NET Core` at the 1.0 release - From Wikipedia

### Benefits of ASP.NET CORE

- Cross-platform development and deployment
- Performance is the main focus
- A simplified hosting model
- Open source (community requested features)
- Modular framework distributed as NuGet packages
- Integrates with popular client-side development frameworks

All the previous versions of .net and visual studio are Windows-specific. As everybody is aware of the news that Visual Studio for Mac and Linux is released. So, the Visual studio became cross-platform. As with visual studio being open source, the compiler of the C# is also becoming open source and is hosted on GitHub the same story is for the .net core as well.

### Drawback of ASP.NET
The hosting model for the ASP.NET is tied to Windows IIS. So, IIS has to handle the request and invokes specific `global.asax` methods.

### Kestrel web server:
Kestrel is a lightweight HTTP server that handles ASP.NET Core applications request pipeline.

With ASP.NET core, there is a Kestrel web server within the asp.net core application which handles the request. So, the IIS will hand off the request to the Kestrel web server and kestrel processes the request. Now, how is this an open source? Yes, it is open source. Instead of IIS, for Mac users, they can have Nginx/Apache as a server to handle the requests and send it to kestrel.

### LTS Support for .NET Core 2.1:
.NET Core 2.1 will be a `Long-term support(LTS)` release. Meaning that this is supported for 3 years. So, it is recommended that we make .NET Core 2.1 as the new standard for .NET Core.

### Contributions from Open Source:
Most of the performance improvements were made to Kestrel HTTP Server. Many of the contributions to performance improvements are from contributors to open source project on GitHub. So, developing on Open source means we should certainly see fixes and features would be way faster to production environments.

### Modularity:
The modularity is achieved as everything on the ASP.NET Core is decoupled from the underlying .NET platform. The web framework (ASP.NET Core) is implemented as modular NuGet packages. So, you can customize, update to whatever you want.

### Integrations with client-side frameworks:
Client-side frameworks these days are getting way popular than the enterprise software. ASP.NET Core integrates with popular client-side frameworks and libraries, including Angular, React, and Bootstrap

### Conclusion:
As one might argue that the Microsoft team is mimicking the node js style of operation in its modularity.  Yes, but Microsoft is trying to build better software to make it better by identifying the drawbacks of the existing applications. So, the heavy frameworks are being modularized.

Back in the day of .NET Core 1.0 initial releases, it was not production ready but as .NET Core 2.1 is LTS release, enterprise products could get support from Microsoft.
