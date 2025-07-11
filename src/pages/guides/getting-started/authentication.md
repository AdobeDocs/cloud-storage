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

Before proceeding, work with your Adobe Representative and ensure you have the following:This tutorial assumes you have worked with your Adobe Representative and have the following:

- An [Adobe Developer Console](https://developer.adobe.com/) account.
- An [Adobe Developer Console project](https://developer.adobe.com/developer-console/docs/guides/projects/projects-empty/) with [OAuth server-to-server](https://developer.adobe.com/developer-console/docs/guides/services/services-add-api-oauth-s2s) or [OAuth user authentication](https://developer.adobe.com/developer-console/docs/guides/services/services-add-api-oauth-user-authentication) credentials for the Adobe Cloud Storage and Collaboration APIs.
- Access to your Client ID and Client Secret from the [Adobe Developer Console project](https://developer.adobe.com/developer-console/docs/guides/services/services-add-api-oauth-s2s#api-overview). Securely store these credentials and never expose them in client-side or public code.

## Authentication Types

The Adobe Cloud Storage and Collaboration API supports both Server-to-Server (OAuth Server-to-Server credentials) and User authentication (OAuth Web App and OAuth Single Page App credentials).

The [Adobe Developer Authentication Guide](https://developer.adobe.com/developer-console/docs/guides/authentication/) has more information about the authentication types and their uses.

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
     --data-urlencode 'scope=openid, AdobeID, offline_access, creative_sdk'
   ```

   A sample response is as follows:

   ```bash
   {
       "access_token": "yourAccessTokenAsdf123",
       "token_type": "bearer",
       "expires_in": 86399
   }
   ```

   The expires_in field indicates the token’s validity in seconds, typically, 24 hours. Your application should securely store the token and refresh it before expiration.

3. Export your access token as an environment variable:

   ```bash
   export CLOUD_STORAGE_ACCESS_TOKEN=yourAccessTokenAsdf123
   ```

### Technical Account Permissions

API requestes made using OAuth Server-to-Server calls are made using a Technical Account. The Technical Account is created automatically when you added Server-to-Server authentication to your Developer Console Project. However, you must grant the Technical Account permissions to allow it to access the Adobe cloud storage conent.

Granting permissions to the Technical Account is done through the [Adobe Admin Console](https://adminconsole.adobe.com/). Note: **You must be an Administrator for your organization to grant permissions to the Technical Account.** The [Getting Started guide](../../getting-started/index.md) has detailed instructions for granting privileges to a Technical Account.

## User Authentication

User authentication is used when your application needs to execute actions on Adobe cloud storage content on behalf of a particular user (with that user's concent).

The Adobe Cloud Storage and Collaboration API supports both OAuth Web App and OAuth Single Page App credentials for user authentication.

The following Adobe Developer documents provide details on User Authentication:

- [User Authentication Implementation Guide](https://developer.adobe.com/developer-console/docs/guides/authentication/UserAuthentication/implementation)
- [User Authentication Credentials API Reference](https://developer.adobe.com/developer-console/docs/guides/authentication/UserAuthentication/ims#fetching-access-tokens)

### User Permissions

API calls made with User authentication use a user token to make the request on behalf of a specific user. The user's [roles and permissions](https://file+.vscode-resource.vscode-cdn.net/Users/mhodgson/Library/CloudStorage/OneDrive-Adobe/Adobe_Projects/Development/torii/docs/public/src/pages/concepts/permissions/index.md) determine what projects, folders, and files can be accessed via the Adobe Cloud Storage and Collaboration API.
