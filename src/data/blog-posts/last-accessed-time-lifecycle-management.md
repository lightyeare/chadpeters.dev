---
title: Hide and seek with Az Blob Last Accessed Time
publishDate: 27 June 2024
description: How do you find an Azure Blob's last accessed time? Read on...
---
_This post was originally published on the [RIMdev Blog](https://rimdev.io/last-accessed-time-lifecycle-management)_

My granddaughter went through a period in her 2s where she liked to play hide and seek. Last week I found myself again playing hide and seek, but this time with an Azure Storage Blob's last accessed time. Don't tell my grandie, but it was a little harder to find the last accessed time than it was to find her 😆.

In my last [post](https://rimdev.io/storage-lifecycle-management) I explained how we were working on lowering our Azure Storage costs by using Lifecycle Management policy. We've been playing with Lifecycle Management policies in our development storage accounts. We recently enabled access time tracking in one those storage accounts. We added a Lifecycle Management policy to use the last access time to move our blobs through the access tiers.  

All files already in the Storage Account when you enable access time will have a null last accessed time. For those blobs Azure uses the policy creation date when applying the policy. Reading or writing a blob updates the last accessed time. I downloaded a few of the blobs via Azure Storage explorer to make them have a last accessed time. To satisfy my curiosity, I opened the properties of a blob in Azure Storage explorer to see the last accessed time. No last access time property 🤔. Since I had my storage account opened in the Azure portal I used the Storage browser blade to open the Blob Container. Browsing to the Blob I used the context menu to view the properties. No last accessed time there either 😕. Apparently last accessed time is a hide and seek pro. 

At this point I was battered and bruised, exhausted from my seeking, and ready to throw in the towel (ok, that may be a bit of an overdramatization). I figured I needed to get closer to the metal so I decided to see what Az Powershell could tell me. For my own future reference, and if it's helpful to you, here are the commands I used:

`Connect-AzAccount` - login and connect to a subscription

`Set-AzCurrentStorageAccount -ResourceGroupName "<resource group name of your storage account>" -Name "<name of storage account>"` - sets the storage context

`$blob = Get-AzStorageBlob -Blob "<name of blob>" -Container "<name of container that has your blob>"` - get the blob for which you want property information

`echo $blob.BlobClient.GetProperties().Value` - show the properties and their values

That returns [BlobProperties](https://learn.microsoft.com/en-us/dotnet/api/azure.storage.blobs.models.blobproperties?view=azure-dotnet) which contains the property `LastAccessed`. 

![Happy Dance](https://i.giphy.com/media/v1.Y2lkPTc5MGI3NjExNnZ0Z2Z5NHdxbDQ2cjlwNHJlaXc1eTdubDNtY3dhbWFyaXUzZ253biZlcD12MV9pbnRlcm5hbF9naWZfYnlfaWQmY3Q9Zw/3ofT5I53iCdlGUjzt6/giphy.gif)

Now I can do a little more testing with last accessed time in our Lifecycle Management policies. Soon we can set up some policies in production and start saving 💲💲. 

> Note: Kudos to the Az Powershell team on their extremely helpful error messages. Check this out - not one but TWO specific suggestions to fix the problem! 
>
> `Get-AzStorageBlob: Could not get the storage context. Please pass in a storage context with "-Context" parameter (can be created with New-AzStorageContext cmdlet), or set the current storage context with Set-AzCurrentStorageAccount cmdlet.` 