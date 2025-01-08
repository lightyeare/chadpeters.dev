---
title: Deploying Node.js to Azure using Visual Studio Code
slug: deploy-nodejs-to-azure
publishDate: 08 January 2025
description: Deploying a Node.js app to Azure is a breeze with Visual Studio Code. 
---

I am finishing up my [learnjavascript.online](https://learnjavascript.online) course, and I am ready to tackle the Final Project 🙌🏻. Instead of using his editor, I thought I would go a step further and spin up an actual website with my project. Since I live in the Microsoft stack, I knew I could create a Web App to host my project. 

> I highly recommend this Javascript course by Jad Joubran. It's been a lot of fun and very informative. I've never formally studied JS, and even though I am a backend developer, I collaborate often with frontend developers. This has allowed me to have much more productive interactions with them.

After a bit of research, I decided to use the [Express application generator](https://expressjs.com/en/starter/generator.html) to easily create a skeleton Node.js application. You can follow along at that URL to setup your Express app. One change that I made was that I installed the EJS template engine. Since I'm basically a full-stack developer now 🙄, I thought I could experiment with a template engine in my final project. 

### Create your App Service ⚙

After your app is created, open the app in Visual Studio Code. To deploy your Node.js app to Azure, you need to have the Azure App Service extension installed. Once that installs, an Azure icon will appear in the VSCode action bar. Click the icon and sign in to Azure. 

Once signed in, open the "RESOURCES" collection. Open a subscription where you want to create your App Service. You will see a list of Resources in your Subscription. Right-click "App Services" and choose "Create New Web App...(Advanced)" 

It's pretty easy to follow the prompts. I chose the F1 Free Tier, Windows, and skipped creating the Application Insights resource. Once you've completed the prompts, Azure will create your new resource.

Creation failed for me the first time. I received an error that a resource provider wasn't registered. If this happens to you, fix it by logging into the Azure portal and selecting the your subscription. Go to Settings-Resource providers and search for the provider in the error message. Select the provider and Register it. Once registered, repeat the creation steps and your App Service will be created.

### Deploy to Azure ➡

Once created, you will see your new App Service listed in the App Services. Right-click your app service and select "Deploy to Web App...". This will deploy the app that is open in your VSCode workspace to the selected App Service. After it deploys, you can select "Browse Website" in the popup that notifies you of deployment completion. 

I expected to see the home page for my skeleton app in the browser, but instead I saw "You do not have permission to view this directory or page." To resolve this, go back to VSCode and open your new web app in the App Service explorer. Right-click the "Application Settings" and add a new setting `SCM_DO_BUILD_DURING_DEPLOYMENT` with a value of `true`. The error happened because there was no web.config in the project root. This application setting enables build automation at deploy time. Build automation detects the start script and uses it to generate the web.config. 

Redeploy your web app and you will see the home page as expected! 🎉 I'm looking forward to completing my final project and creating my first JS website. 

