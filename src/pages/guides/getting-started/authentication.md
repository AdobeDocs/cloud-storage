---
title: Authentication - Adobe Cloud Storage and Collaboration API
description: Adobe Cloud Storage and Collaboration API authentication guide
keywords:
  - Adobe Cloud Storage and Collaboration
  - Adobe Cloud Storage and Collaboration API
  - Adobe Cloud Storage
  - Creative production workflows
  - Adobe workflows
  - Cloud Storage endpoints
  - Cloud Storage authentication and autorization
  - API Key
  - Client ID
contributors:
  - https://github.com/michael-hodgson
  - https://github.com/lijumjose
layout: none
---

# Authentication setup

Learn how to authenticate requests to the Adobe Cloud Storage and Collaboration API.

## Overview

Every request made to Adobe Cloud Storage and Collaboration APIs must include an encrypted access token. Your secure, server-side application retrieves an access token by making a request to the [Adobe Identity Management System (IMS)](https://www.adobe.com/content/dam/cc/en/trust-center/ungated/whitepapers/corporate/adobe-identity-management-services-security-overview.pdf), using your Client ID and Client Secret.

## Prerequisites

This document assumes you have worked with your organization's Administrator and have the following:

- A user account with a Developer role authorized for an Adobe product that uses Enterprise Storage. This grants access to the [Adobe Developer Console](https://developer.adobe.com/).
- An [Adobe Developer Console project](https://developer.adobe.com/developer-console/docs/guides/projects/projects-empty/) configured with either OAuth server-to-server or OAuth user authentication credentials for the Adobe Cloud Storage and Collaboration APIs.
- Access to your Client ID and Client Secret from the Adobe Developer Console project. Securely store these credentials and never expose them in client-side or public code.

For more information about setting up an Adobe Developer Console project, see [Adobe Developer Console](./developer-console.md)

## Authentication Types

The Adobe Cloud Storage and Collaboration API supports the following authentication methods:

- Server-to-Server authentication using OAuth Server-to-Server credentials
- User authentication using OAuth Web App and OAuth Single Page App credentials

Each method is designed for different use cases, depending on whether your application acts independently or on behalf of a user.

For a detailed comparison and implementation guidance, refer to [Adobe Developer Authentication Guide](https://developer.adobe.com/developer-console/docs/guides/authentication/).

## Server-to-Server Authentication

Use Server-to-Server authentication if your application needs to execute actions on Adobe cloud storage content without involving a user.

### Retrieve a Server-to-Server access token

Perform the following steps:

1. Open a secure terminal and export your **Client ID** and **Client Secret** as environment variables:

   ```bash
   export CLOUD_STORAGE_CLIENT_ID=yourClientIdAsdf123
   export CLOUD_STORAGE_CLIENT_SECRET=yourClientSecretAsdf123
   ```

2. Run the following command to generate an access token:

   ```bash
   curl --location 'https://ims-na1.adobelogin.com/ims/token/v3' \
     --header 'Content-Type: application/x-www-form-urlencoded' \
     --data-urlencode 'grant_type=client_credentials' \
     --data-urlencode "client_id=$CLOUD_STORAGE_CLIENT_ID" \
     --data-urlencode "client_secret=$CLOUD_STORAGE_CLIENT_SECRET" \
     --data-urlencode 'scope=openid, AdobeID, creative_sdk, cloud_storage_collab_api'
   ```

   A sample response is as follows:

   ```bash
   {
       "access_token": "yourAccessTokenAsdf123",
       "token_type": "bearer",
       "expires_in": 86399
   }
   ```

   The `expires_in` field indicates the token’s validity in seconds, typically, 24 hours. Your application should securely store the token and refresh it before expiration.

3. Export your access token as an environment variable:

   ```bash
   export CLOUD_STORAGE_ACCESS_TOKEN=yourAccessTokenAsdf123
   ```

### Technical Account Permissions

When using OAuth Server-to-Server authentication, all API requests are executed through a Technical Account. This account is automatically created when you configure Server-to-Server authentication in your Adobe Developer Console project.

However, to enable access to Adobe cloud storage content, you must explicitly grant permissions to the Technical Account.

Granting permissions to the Technical Account is done through the [Adobe Admin Console](https://adminconsole.adobe.com/).

**Note:** You must be an Administrator for your organization to grant permissions to the Technical Account.

For step-by-step instructions, refer to the [Technical Account setup guide](./technical-account-setup.md).

## User Authentication

User authentication is used when your application needs to execute actions on Adobe cloud storage content on behalf of a particular user (with that user's consent).

The Adobe Cloud Storage and Collaboration API supports both OAuth Web App and OAuth Single Page App credentials for user authentication.

The following Adobe Developer documents provide details on User Authentication:

- [User Authentication Implementation Guide](https://developer.adobe.com/developer-console/docs/guides/authentication/UserAuthentication/implementation)
- [User Authentication Credentials API Reference](https://developer.adobe.com/developer-console/docs/guides/authentication/UserAuthentication/ims#fetching-access-tokens)

### User permissions

API calls made with User authentication use a user token to make the request on behalf of a specific user. The user's [roles and permissions](https://file+.vscode-resource.vscode-cdn.net/Users/mhodgson/Library/CloudStorage/OneDrive-Adobe/Adobe_Projects/Development/torii/docs/public/src/pages/concepts/permissions/index.md) determine what projects, folders, and files can be accessed using the Adobe Cloud Storage and Collaboration API.
