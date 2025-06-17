---
title: Resolving Package Dependencies for Security Vulnerabilities
publishDate: 17 June 2025
description: Tips and tools for resolving package dependencies
slug: resolving-package-vulnerabilities
---

At the beginning of my career managing references in your projects was time-consuming and not fun. With the advent of package managers, better tooling, and teammates who handled the chore, I stopped paying attention. 😬 This week I had the opportunity to dive back in. Good news is that it definitely wasn't as painful as I remember. I'm pretty sure I had to walk uphill both ways in the snow back in day. 

We use a product called [Mend Bolt](https://www.mend.io/free-developer-tools/bolt/) as part of our build process. Mend Bolt scans our packages for security vulnerabilities in our open source dependencies.  I recently encountered this report:
![Mend Bolt report](/assets/blog/packages/mendbolt_report.jpg)

You can click the links and view information about the vulnerabilities. It also provides a recommendation to upgrade Newtonsoft.Json to 13.0.1. I knew from work in the past, upgrading Newtonsoft.Json to the recommended version would likely bring in a new version of the offending package. If Newtonsoft.Json is not installed, I could install it. Perhaps there is a package that is using a lower version, and explicitly installing a newer version will cause the new version to be used and resolve the vulnerability. 

My solution has multiple projects so I needed to figure out which project required the adjustment. In JetBrains Rider, I selected "Manage Nuget Packages" on my solution. I entered "microsoft.extensions.apidescription.server" in the search box:
![Package search results](/assets/blog/packages/package_search.jpg)

We can see that this package is _implicitly_ installed and it's being used in AgentService.WebAPI. Implicit means that it is being referenced by an installed package, and not set explicitly in the project file. The referencing package controls the version that is being referenced.

I installed Newtonsoft.Json 13.0.1 in AgentService.WebAPI, pushed my commit, and...still the same report of a vulnerability. 😟 

Back in my solution, in the Solution Explorer I opened the Packages for WebAPI. I double-clicked on the Newtonsoft.Json assembly to open the assembly in the Assembly Explorer:
![Package search results](/assets/blog/packages/package_reference.jpg)

In the Assembly Explorer I opened the References for Newtonsoft.Json and could see that "Microsoft.Extensions.ApiDescription.Server" was not listed. I headed to the project.assets.json file in the AgentService.WebAPI/obj folder and searched for "Microsoft.Extensions.ApiDescription.Server" there. I found it listed as a dependency for the explicitly installed Swashbuckle.AspNetCore package:
![project.assets.json file](/assets/blog/packages/project_assets.jpg)

I removed the Newtonsoft.Json package from my project, and upgraded the Swashbuckle.AspNetCore package to the most recent package. This brought "Microsoft.Extensions.ApiDescription.Server" to version 8.0.0. I pushed my commit, and the vulnerability report showed all clear. 🎉 
