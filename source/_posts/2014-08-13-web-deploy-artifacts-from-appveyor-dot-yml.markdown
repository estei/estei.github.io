---
layout: post
title: "Web Deploy artifacts from appveyor.yml"
date: 2014-08-13 19:55:05 +0200
comments: true
categories: appveyor artifacts WebDeployPackage
published: false
---
At work we have started using [Appveyor](http://appveyor.com) for building and deploying websites.
This has led me to spend quite a bit of time with the configuration file appveyor.yml.
Today I ran into a little quirk of the undocumented type. 

The project I am currently working on uses Web Deploy to deploy an Umbraco website to a Microsoft Azure Website.
When the project builds a Web Deploy package is created and saved to appveyor's artifact storage. 

``` yaml Upload Web Deploy Package
artifacts:

  # pushing the website package
  - path: build\_PublishedWebsites\Website_Package\Website.zip
    name: WebsitePackage
```

As you can see in the picture below, the zip file was saved with the deployment name WebsitePackage.
Everything looked like it should, happy days.

{% img /images/web-deploy-zip.png 'Artifact as zip' 'Artifact saved as zip archive' %}

Deployment to different environments  in Appveyor is so easy. 
It has a Web Deploy provider that is super easy to [use](http://www.appveyor.com/docs/deployment/web-deploy).
But when I ran the deploy everything wasn't right, it looked like the zip file had just been unzipped directly into the wwwroot directory.

**image of files being wrong**

**What was wrong**
**not a WebDeploy package in appveyor**

**Added type: WebDeployPackage**
``` yaml With type
artifacts:

  # pushing the website package
  - path: build\_PublishedWebsites\Website_Package\Website.zip
    name: WebsitePackage
    type: WebDeployPackage
```

{% img /images/web-deploy-webdeploy.png [Artifact as zip [Artifact saved as zip archive]] %}

**Files are right**
**image of files being right**