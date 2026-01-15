---
title: Adobe Cloud Storage and Collaboration API - API Reference
description: Adobe Cloud Storage and Collaboration API reference
keywords:
  - Adobe Cloud Storage and Collaboration
  - Adobe Cloud Storage and Collaboration API
  - Adobe Cloud Storage
  - Creative production workflows
  - Adobe workflows
  - API Reference
contributors:
  - https://github.com/michael-hodgson
  - https://github.com/lmjose
layout: none
---

# API reference

The Adobe Cloud Storage and Collaboration API provides programmatic access to content stored in Adobe Cloud Storage for your organization. With the Adobe Cloud Storage and Collaboration API, you can interact with projects, folders, and files to automate creative production workflows and integrate Adobe Cloud Storage with your existing systems.

## Connecting to the Cloud Storage and Collaboration API

Before using the Cloud Storage and Collaboration API, you must create a development project in the [Adobe Developer Console](https://developer.adobe.com/). This integration registers your application as a client of the Cloud Storage and Collaboration API, and provides the credentials required to authorize API calls.

- For setup instructions, see [Developer Console](../getting-started/developer-console.md)
- For information about client credential authentication and retrieving Client ID, Client Secret, Scopes, or access tokens, see [Authentication Setup](../getting-started/authentication.md).

## Cloud Storage API Calls

Applications can use the API to access and manage projects, folders, and files in Adobe enterprise cloud storage. You can create and list projects, assign user roles, manage folders, upload and download files, and more.

For more information on content structures, see [Cloud Storage and Collaboration Concepts](../overview/constructs.md).

All API requests should be directed to the Cloud Storage and Collaboration API server:

```bash
https://cloudstorage.adobe.io/v1
```

## Summary of Actions on Cloud Content

### Projects

Projects are durable spaces used by teams to organize and collaborate on cloud documents and related cloud content.

| Task                                                                                                           | Operation | Endpoint                                 | Description                                                                                                                |
| -------------------------------------------------------------------------------------------------------------- | --------- | ---------------------------------------- | -------------------------------------------------------------------------------------------------------------------------- |
| [Get list of projects](./specification.md#operation/getProjects)                                               | GET       | /projects                                | Returns a paginated list of all projects within the organization's enterprise shared storage                               |
| [Create a new project](./specification.md#operation/createProject)                                             | POST      | /projects                                | Creates a new project within the organization's enterprise shared storage                                                  |
| [Get a project's metadata](./specification.md#operation/getProject)                                            | GET       | /projects/{assetId}                      | Retrieves full metadata for a specific project                                                                             |
| [Discard a project](./specification.md#operation/discardProject)                                               | POST      | /projects/{assetId}/discard              | Marks a project for discard (soft deletion) along with all its descendant assets                                           |
| [List the contents of a project](./specification.md#operation/getProjectAssets)                                | GET       | /projects/{assetId}/children             | Retrieves a paginated list of top-level children (folders, files, or other resources) contained within a specified project |
| [Get the current user's permissions for a project](./specification.md#operation/getProjectEffectivePermission) | GET       | /projects/{assetId}/effective-permission | Retrieves the effective permission (role) assigned to the user for a specific project                                      |
| [Get all the permissions for a project](./specification.md#operation/getProjectPermissions)                    | GET       | /projects/{assetId}/permissions          | Retrieves the explicit roles and permissions assigned to all users and groups for a specific project                       |
| [Set the permissions for a project](./specification.md#operation/patchProjectPermissions)                      | PATCH     | /projects/{assetId}/permissions          | Adds, updates, or removes direct permissions of a specific project                                                         |
| [Package a project and its files](./specification.md#operation/packageProject)                                 | POST      | /projects/{assetId}/package              | Packages a project, and all its contents, into a downloadable format                                                       |

### Folders

Folders can contain files and other folders, enabling logical organization of assets.

| Task                                                                                                          | Operation | Endpoint                                | Description                                                                                                               |
| ------------------------------------------------------------------------------------------------------------- | --------- | --------------------------------------- | ------------------------------------------------------------------------------------------------------------------------- |
| [Create a folder](./specification.md#operation/createFolder)                                                  | POST      | /folders                                | Creates a folder under a specified parent container (project or another folder)                                           |
| [Get a folder's metadata](./specification.md#operation//getFolder)                                            | GET       | /folders/{assetId}                      | Retrieves full metadata for a specific Folder                                                                             |
| [List the contents of a folder](./specification.md#operation/getFolderAssets)                                 | GET       | /folders/{assetId}/children             | Retrieves a paginated list of top-level children (folders, files, or other resources) contained within a specified folder |
| [Get the current user's permissions for a project](./specification.md#operation/getFolderEffectivePermission) | GET       | /folders/{assetId}/effective-permission | Retrieves the effective permission (role) assigned to the user for a specific folder                                      |

### Files

Files are used to store content in Adobe cloud storage. See [Cloud Storage and Collaboration Concepts](../overview/constructs.md#file) for more information about files and file types.

| Task                                                                                                     | Operation | Endpoint                              | Description                                                                                                                               |
| -------------------------------------------------------------------------------------------------------- | --------- | ------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------- |
| [Get file metadata](./specification.md#operation/getFile)                                                | GET       | /files/{assetId}                      | Retrieves full metadata for a specific file                                                                                               |
| [Get the current user's permissions for a file](./specification.md#operation/getFileEffectivePermission) | GET       | /files/{assetId}/effective-permission | Retrieves the effective permission (role) assigned to the user for a specific file                                                        |
| [Download a file](./specification.md#operation/downloadFile)                                             | GET       | /files/{assetId}/download             | Creates pre-signed URLs for downloading a file                                                                                            |
| [Get an image rendition of a file](./specification.md#operation/getFileImageRendition)                   | GET       | /files/{assetId}/image-rendition      | Returns an image rendition of the specified file                                                                                          |
| [Initiate a file upload](./specification.md#operation/initBlockBasedFileUpload)                          | POST      | /files/upload/init                    | Initiates the uploading of a file to Adobe cloud storage. Supports multiple blocks for large files                                        |
| [Complete a file upload](./specification.md#operation/finalizeBlockBasedFileUpload)                      | POST      | /files/upload/finalize                | Finalizes the block upload process by initiating an asynchronous job that will merge all uploaded blocks and creates the final file asset |

### Jobs Operations

Manages asynchronous jobs in the system.

| Task                                                                               | Operation | Endpoint        | Description                                         |
| ---------------------------------------------------------------------------------- | --------- | --------------- | --------------------------------------------------- |
| [Get the status of an asynchronous job](./specification.md#operation/getJobStatus) | GET       | /status/{jobId} | Retrieves the current status of an asynchronous job |

## What's next?

Explore [Adobe Cloud Storage and Collaboration API specifications](./specification.md).
