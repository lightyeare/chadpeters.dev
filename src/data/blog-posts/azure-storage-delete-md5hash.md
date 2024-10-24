---
title: Delete MD5 Hash from Azure Storage Blob using Azure CLI
slug: azure-storage-delete-md5hash
publishDate: 23 October 2024
description: Should you search first or read the docs first?
---

I've been working on completing the project I talked about in my [last post.](https://chadpeters.dev/blog/remove-file-extension-from-string) WAV files get dropped into an Azure storage account and then we copy them to another account. After copying the files I check their MD5 hashes to confirm the copy. I noticed that some files have MD5 hashes, and some don't. I wanted to replicate this in our DEV environment for testing, but every time I uploaded a file Azure created the MD5 hash.

I found a number of search results that suggested various ways to delete the MD5 hash of a blob. These included using `--set` and `--remove` flags. `--remove` was touted as an "undocumented feature." 😂 I also tried various values for the `--content-md5` flag without happy results. After giving up on searching and then turning to the documentation I found the answer. 

```csharp
az storage blob update --container-name <container_name> --name <blob_name> 
	--clear-content-settings true --content-type audio/wav
```

From the [docs](https://learn.microsoft.com/en-us/cli/azure/storage/blob?view=azure-cli-latest#az-storage-blob-update) for `--clear-content-settings`

> If this flag is set, then if any one or more of the following properties (--content-cache-control, --content-disposition, --content-encoding, --content-language, --content-md5, --content-type) is set, then all of these properties are set together. If a value is not provided for a given property when at least one of the properties listed below is set, then that property will be cleared.

Sometimes it's better to just slow down and read the documentation first before wasting a bunch of time googling the (wrong) answer. 



