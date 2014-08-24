---
layout: post
title: "Web Deploy artifacts from appveyor.yml"
date: 2014-08-24 18:33:05 +0200
comments: true
categories: appveyor WebDeployPackage msdeploy 
---
At work we have started using [Appveyor](http://appveyor.com) for building and deploying websites.
This has led me to spend quite a bit of time with the configuration file appveyor.yml.
Today I ran into a little quirk of the undocumented type. 

The project I am currently working on uses Web Deploy to deploy an Umbraco website to a Microsoft Azure Website.
When the project builds, a Web Deploy package is created and saved to appveyor's artifact storage. 

Earlier I have configured our builds in the web interface and I have been quite happy with it. 
But I wanted to try the power of the [appveyor.yml](http://www.appveyor.com/docs/appveyor-yml) file.
The yaml file allows you a greater amount of control over the build, and allows you to put the build configuration in source control with your projects.
This way you can clone your project and not have to set up a new build in appveyor from scratch.

It is quite easy to configure an artifact upload in yaml.

``` yaml Upload Web Deploy Package
artifacts:
  # pushing the website package
  - path: build\_PublishedWebsites\Website_Package\Website.zip
    name: WebsitePackage
```

As you can see in the picture below, the zip file was saved with the deployment name WebsitePackage.
Everything looked like it should. Happy days!

{% img /images/web-deploy-zip.png 'Artifact as zip' 'Artifact saved as zip archive' %}

Deployment to different environments  in Appveyor is so easy. 
It has a Web Deploy provider that is super easy to [use](http://www.appveyor.com/docs/deployment/web-deploy).
But when I ran the deploy something was wrong, it looked like the zip file had just been unzipped directly into the wwwroot directory.
This meant that all the files for the website were placed in a folder some nine directory levels deep at 
`..\wwwroot\Content\C_C\projects\"solutiondir"\"projectdir"\obj\Release\Package\PackageTmp\`
I wasn't really sure what was going on, as I had had this same deploy work where I had configured it via the web interface.

After some searching around, I found a difference between the two setups. 
When I configured the build through the web interface I had set the artifact type for the zip file to "Web Deploy Package".

{% img /images/artifact-web-deploy.png 'Artifact as Web Deploy Package' 'Artifact set to Web Deploy Package in web interface' %}

I couldn't find a way to do this in the yaml file. I searched through the otherwise great [documentation](http://www.appveyor.com/docs), 
but found nothing about how to set an artifact as a web deploy package in the appveyor.yml file.

After a bit of trial and error and some [fiddling](http://www.telerik.com/fiddler) around with the web interface, I found out how to do it in appveyor.yml.
You just need to set the type of the artifact to "WebDeployPackage"

``` yaml With type
artifacts:
  # pushing the website package
  - path: build\_PublishedWebsites\Website_Package\Website.zip
    name: WebsitePackage
    type: WebDeployPackage
```

Now the artifact type in the "Artifacts" tab shows as a Webdeploy package.
{% img /images/web-deploy-webdeploy.png [Artifact as zip [Artifact saved as zip archive]] %}

That made everything right. Now when I run the deploy with the package the right files are placed in the wwwroot directory, not in some deep deep subdirectory.

Short version:

If you want to have web deploy working right when deploying from appveyor, then set the type of your package to `type: WebDeployPackage`.