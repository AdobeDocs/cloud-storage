---
title: Getting Started - Adobe Cloud Storage and Collaboration API
description: Adobe Cloud Storage and Collaboration API getting started
keywords:
  - Adobe Cloud Storage and Collaboration
  - Adobe Cloud Storage and Collaboration API
  - Adobe Cloud Storage
  - Creative production workflows
  - Adobe workflows
  - Getting Started
contributors:
  - https://github.com/michael-hodgson
  - https://github.com/lijumjose
layout: none
---

# Getting started with the Adobe Cloud Storage and Collaboration API

The Adobe Cloud Storage and Collaboration API gives you programmatic access to content stored in Adobe cloud storage for enterprises. With the Adobe Cloud Storage and Collaboration API, you can create and list projects, set user roles, create project folders, upload/download project files, and more to automate creative production workflows and integrate Adobe cloud storage with your existing systems.

## Prerequisites

Before you can use the Cloud Storage and Collaboration API, you must use the [Adobe Developer Console](https://developer.adobe.com/) to create a development project. The integration registers your application as a client of the Cloud Storage and Collaboration API, and gives you the credentials you need to authorize calls to the API.

- For information on setting up an Adobe Developer Console project, see [Developer Console](./developer-console.md)
- For information about client credential authentication and retrieving Client ID, Client Secret, Scopes, or access tokens, see [Authentication Setup](./authentication.md).

## Make your first API call

Once you have created your access token, you can follow the steps below to make your first API call.

- Open your terminal and paste the code below.
- Replace the variables `<YOUR_ACCESS_TOKEN>` with the token you generated on the Adobe Developer Console.
- Replace `<YOUR_CLIENT_ID>`. You can find this on the same page you generated your token on.
- Once all variables have been replaced you can run the command.

```bash
curl --request GET \
  --url 'https://cloudstorage.adobe.io/v1//projects?limit=20' \
  --header 'Authorization: Bearer <YOUR_ACCESS_TOKEN>' \
  --header 'Content-Type: application/vnd.adobecloud.directory+json' \
  --header 'x-api-key: <YOUR_CLIENT_ID>'
```

Congratulations! You yave just made your first request to the Cloud Storage and Collaboration API.

## Deepen your understanding

Explore the [Working with Cloud Storage and Collaboration APIs](../quick-start/index.md) tutorial for a working example of the Cloud Storage and Collaboration API's capabilities.

See the [API Reference](../../api/index.md) for details about the Cloud Storage and Collaboration API functions
