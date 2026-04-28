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
  - https://github.com/irwin-dolobowsky-adobe
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

| Task                                                                                                           | Operation | Endpoint                                         | Description                                                                                                                |
| -------------------------------------------------------------------------------------------------------------- | --------- | ------------------------------------------------ | -------------------------------------------------------------------------------------------------------------------------- |
| [Get list of projects](./specification.md#operation/getProjects)                                               | GET       | `/projects`                                      | Returns a paginated list of all projects within the organization's enterprise shared storage                               |
| [Create a new project](./specification.md#operation/createProject)                                             | POST      | `/projects`                                      | Creates a new project within the organization's enterprise shared storage                                                  |
| [Get a project's metadata](./specification.md#operation/getProject)                                            | GET       | `/projects/{assetId}`                            | Retrieves full metadata for a specific project                                                                             |
| [Delete a project](./specification.md#operation/deleteProject)                                                 | DELETE    | `/projects/{assetId}`                            | Deletes a project by ID, along with all its descendant assets                                                              |
| [Rename a project](./specification.md#operation/renameProject)                                                 | POST      | `/projects/{assetId}/rename`                     | Renames a project                                                                                                          |
| [Restore a project](./specification.md#operation/restoreProject)                                               | POST      | `/projects/{assetId}/restore`                    | Restores a soft-deleted project                                                                                            |
| [List the contents of a project](./specification.md#operation/getProjectChildren)                              | GET       | `/projects/{assetId}/children`                   | Retrieves a paginated list of top-level children (folders, files, or other resources) contained within a specified project |
| [Get the current user's permissions for a project](./specification.md#operation/getProjectEffectivePermission) | GET       | `/projects/{assetId}/effective-permission`       | Retrieves the effective permission (role) assigned to the user for a specific project                                      |
| [Get all roles for a project](./specification.md#operation/getProjectRoles)                                    | GET       | `/projects/{assetId}/roles`                      | Retrieves the explicit roles assigned to all users and groups for a specific project                                       |
| [Set roles for a project](./specification.md#operation/patchProjectRoles)                                      | PATCH     | `/projects/{assetId}/roles`                      | Adds, updates, or removes roles for a specific project                                                                     |
| [Add a brand reference to a project](./specification.md#operation/addProjectReference)                         | POST      | `/projects/{projectId}/references`               | Adds a reference (link) to a brand within a project. A project can have a maximum of 200 references                        |
| [List brand references for a project](./specification.md#operation/listProjectReferences)                      | GET       | `/projects/{projectId}/references`               | Returns a list of brand references associated with the specified project                                                   |
| [Remove a brand reference from a project](./specification.md#operation/removeProjectReference)                 | DELETE    | `/projects/{projectId}/references/{referenceId}` | Removes a brand reference from a project                                                                                   |

### Folders

Folders can contain files and other folders, enabling logical organization of assets.

| Task                                                                                                         | Operation | Endpoint                                  | Description                                                                                                               |
| ------------------------------------------------------------------------------------------------------------ | --------- | ----------------------------------------- | ------------------------------------------------------------------------------------------------------------------------- |
| [Create a folder](./specification.md#operation/createFolder)                                                 | POST      | `/folders`                                | Creates a folder under a specified parent container (project or another folder)                                           |
| [Get a folder's metadata](./specification.md#operation/getFolder)                                            | GET       | `/folders/{assetId}`                      | Retrieves full metadata for a specific folder                                                                             |
| [Delete a folder](./specification.md#operation/deleteFolder)                                                 | DELETE    | `/folders/{assetId}`                      | Deletes a folder by ID, along with all its descendant assets                                                              |
| [Rename a folder](./specification.md#operation/renameFolder)                                                 | POST      | `/folders/{assetId}/rename`               | Renames a folder                                                                                                          |
| [Restore a folder](./specification.md#operation/restoreFolder)                                               | POST      | `/folders/{assetId}/restore`              | Restores a soft-deleted folder                                                                                            |
| [List the contents of a folder](./specification.md#operation/getFolderChildren)                              | GET       | `/folders/{assetId}/children`             | Retrieves a paginated list of top-level children (folders, files, or other resources) contained within a specified folder |
| [Get the current user's permissions for a folder](./specification.md#operation/getFolderEffectivePermission) | GET       | `/folders/{assetId}/effective-permission` | Retrieves the effective permission (role) assigned to the user for a specific folder                                      |

### Files

Files are used to store content in Adobe cloud storage. See [Cloud Storage and Collaboration Concepts](../overview/constructs.md#file) for more information about files and file types.

| Task                                                                                                     | Operation | Endpoint                                | Description                                                                                                                               |
| -------------------------------------------------------------------------------------------------------- | --------- | --------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------- |
| [Get file metadata](./specification.md#operation/getFile)                                                | GET       | `/files/{assetId}`                      | Retrieves full metadata for a specific file                                                                                               |
| [Delete a file](./specification.md#operation/deleteFile)                                                 | DELETE    | `/files/{assetId}`                      | Deletes a file by ID                                                                                                                      |
| [Rename a file](./specification.md#operation/renameFile)                                                 | POST      | `/files/{assetId}/rename`               | Renames a file                                                                                                                            |
| [Restore a file](./specification.md#operation/restoreFile)                                               | POST      | `/files/{assetId}/restore`              | Restores a soft-deleted file                                                                                                              |
| [Get the current user's permissions for a file](./specification.md#operation/getFileEffectivePermission) | GET       | `/files/{assetId}/effective-permission` | Retrieves the effective permission (role) assigned to the user for a specific file                                                        |
| [Get all roles for a file](./specification.md#operation/getFileRoles)                                    | GET       | `/files/{assetId}/roles`                | Retrieves the explicit roles assigned to all users and groups for a specific file                                                         |
| [Set roles for a file](./specification.md#operation/patchFileRoles)                                      | PATCH     | `/files/{assetId}/roles`                | Adds, updates, or removes roles for a specific file                                                                                       |
| [Copy a file](./specification.md#operation/copyFile)                                                     | POST      | `/files/{assetId}/copy`                 | Copies a file to a new location. Returns a job ID to track the asynchronous operation                                                     |
| [Move a file](./specification.md#operation/moveFile)                                                     | POST      | `/files/{assetId}/move`                 | Moves a file to a new location. Returns a job ID to track the asynchronous operation                                                      |
| [Download a file](./specification.md#operation/downloadFile)                                             | GET       | `/files/{assetId}/download`             | Creates pre-signed URLs for downloading a file                                                                                            |
| [Get an image rendition of a file](./specification.md#operation/getFileImageRendition)                   | GET       | `/files/{assetId}/image-rendition`      | Returns an image rendition of the specified file                                                                                          |
| [Initiate a file upload](./specification.md#operation/initBlockBasedFileUpload)                          | POST      | `/files/upload/init`                    | Initiates the uploading of a file to Adobe cloud storage. Supports multiple blocks for large files                                        |
| [Complete a file upload](./specification.md#operation/finalizeBlockBasedFileUpload)                      | POST      | `/files/upload/finalize`                | Finalizes the block upload process by initiating an asynchronous job that will merge all uploaded blocks and creates the final file asset |
| [Initiate a file replacement upload](./specification.md#operation/initBlockBasedFileReplacement)         | POST      | `/files/{assetId}/upload/init`          | Initiates uploading a new version of an existing file. Supports multiple blocks for large files                                           |
| [Complete a file replacement upload](./specification.md#operation/finalizeBlockBasedFileReplacement)     | POST      | `/files/{assetId}/upload/finalize`      | Finalizes the block upload process and replaces the existing file with the newly uploaded version                                         |

### Brands

Brands are collections of reusable assets and guidelines for content reuse across documents. They contain elements such as logos, colors, fonts, and graphics.

| Task                                                                                                       | Operation | Endpoint                                         | Description                                                                                                     |
| ---------------------------------------------------------------------------------------------------------- | --------- | ------------------------------------------------ | --------------------------------------------------------------------------------------------------------------- |
| [Create a brand](./specification.md#operation/createBrand)                                                 | POST      | `/brands`                                        | Creates an empty brand within a specified project                                                               |
| [Get a brand's metadata](./specification.md#operation/getBrand)                                            | GET       | `/brands/{assetId}`                              | Retrieves full metadata for a specific brand                                                                    |
| [Delete a brand](./specification.md#operation/deleteBrand)                                                 | DELETE    | `/brands/{assetId}`                              | Soft or permanently deletes a brand by ID                                                                       |
| [Rename a brand](./specification.md#operation/renameBrand)                                                 | POST      | `/brands/{assetId}/rename`                       | Renames a brand                                                                                                 |
| [List elements in a brand](./specification.md#operation/listBrandElements)                                 | GET       | `/brands/{assetId}/elements`                     | Returns a paginated list of elements inside the specified brand                                                 |
| [Initialize a brand element binary upload](./specification.md#operation/initBrandElementUpload)            | POST      | `/brands/{assetId}/elements/init`                | Initializes a block-based upload session for a new binary element (logos, images) within a brand                |
| [Finalize a brand element binary upload](./specification.md#operation/finalizeBrandElementUpload)          | POST      | `/brands/{assetId}/elements/finalize`            | Finalizes a block-based binary upload for a brand element. Returns a job ID to track the asynchronous operation |
| [Create a JSON brand element](./specification.md#operation/createJsonBrandElement)                         | POST      | `/brands/{assetId}/elements/content`             | Creates a JSON-based element (font definition or color theme) within a brand synchronously                      |
| [Get a brand element](./specification.md#operation/getBrandElement)                                        | GET       | `/brands/{assetId}/elements/{elementId}`         | Returns detailed information about a single element inside a brand                                              |
| [Delete a brand element](./specification.md#operation/deleteBrandElement)                                  | DELETE    | `/brands/{assetId}/elements/{elementId}`         | Deletes an element from the specified brand                                                                     |
| [Get the content of a JSON brand element](./specification.md#operation/getBrandElementContent)             | GET       | `/brands/{assetId}/elements/{elementId}/content` | Returns the inline JSON content of a brand element (fonts, color themes)                                        |
| [Get all roles for a brand](./specification.md#operation/getBrandRoles)                                    | GET       | `/brands/{assetId}/roles`                        | Retrieves the explicit roles assigned to all users and groups for a specific brand                              |
| [Set roles for a brand](./specification.md#operation/patchBrandRoles)                                      | PATCH     | `/brands/{assetId}/roles`                        | Adds, updates, or removes roles for a specific brand                                                            |
| [Get the current user's permissions for a brand](./specification.md#operation/getBrandEffectivePermission) | GET       | `/brands/{assetId}/effective-permission`         | Retrieves the effective permission (role) assigned to the user for a specific brand                             |

### Jobs Operations

Manages asynchronous jobs in the system.

| Task                                                                               | Operation | Endpoint          | Description                                         |
| ---------------------------------------------------------------------------------- | --------- | ----------------- | --------------------------------------------------- |
| [Get the status of an asynchronous job](./specification.md#operation/getJobStatus) | GET       | `/status/{jobId}` | Retrieves the current status of an asynchronous job |

## What's next?

Explore [Adobe Cloud Storage and Collaboration API specifications](./specification.md).
