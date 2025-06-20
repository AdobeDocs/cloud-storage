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
layout: none
---

# Authentication

Learn how to authenticate requests to the Adobe Cloud Storage and Collaboration API.

## Overview

Every request made to Adobe Cloud Storage and Collaboration APIs must include an encrypted access token. Your secure, server-side application retrieves an access token by making a request to the [Adobe Identity Management System (IMS)](https://www.adobe.com/content/dam/cc/en/trust-center/ungated/whitepapers/corporate/adobe-identity-management-services-security-overview.pdf) with your Client ID and Client Secret.

## Prerequisites

This tutorial assumes you have worked with your Adobe Representative and have the following:

- An [Adobe Developer Console](https://developer.adobe.com/) account.
- A [Adobe Developer Console project](https://developer.adobe.com/developer-console/docs/guides/projects/projects-empty/) with  Adobe Cloud Storage and Collaboration API [OAuth Server-to-Server](https://developer.adobe.com/developer-console/docs/guides/services/services-add-api-oauth-s2s) or [OAuth User authentication](https://developer.adobe.com/developer-console/docs/guides/services/services-add-api-oauth-user-authentication) credentials set up.
- Access to your Client ID and Client Secret from the [Adobe Developer Console project](https://developer.adobe.com/developer-console/docs/guides/services/services-add-api-oauth-s2s#api-overview). Securely store these credentials and never expose them in client-side or public code.

## Retrieve a Server-to-Server access token

First, open a secure terminal and export your **Client ID** and **Client Secret** as environment variables so that your later commands can access them:

```bash
export CLOUD_STORAGE_CLIENT_ID=yourClientIdAsdf123
export CLOUD_STORAGE_CLIENT_SECRET=yourClientSecretAsdf123
```

Next, run the following command to generate an access token:

```bash
curl --location 'https://ims-na1.adobelogin.com/ims/token/v3' \
--header 'Content-Type: application/x-www-form-urlencoded' \
--data-urlencode 'grant_type=client_credentials' \
--data-urlencode "client_id=$CLOUD_STORAGE_CLIENT_ID" \
--data-urlencode "client_secret=$CLOUD_STORAGE_CLIENT_SECRET" \
--data-urlencode 'scope=openid, AdobeID, offline_access, creative_sdk'
```

The response will look like this:

```bash
{"access_token":"yourAccessTokenAsdf123","token_type":"bearer","expires_in":86399}
```

Notice how the response includes an expires_in field, which informs you of how many more seconds the access token is valid for. Each access token is valid for 24 hours, after which your secure server-side application will need to request a new token. A best practice is securely store the token and refresh it before it expires.

Export your access token as an environment variable:

```bash
export FIREFLY_SERVICES_ACCESS_TOKEN=yourAccessTokenAsdf123
```

### Technical Account Privileges

===TODO: add section about making the TA a storage admin===

## Retrieve a User access token

===TODO: add section about configuring for a User access token===
===TODO: redirect to [This link](https://developer.adobe.com/developer-console/docs/guides/authentication/UserAuthentication/ims#fetching-access-tokens)

![Choose user authentication](../images/choose-user-auth.jpg)
