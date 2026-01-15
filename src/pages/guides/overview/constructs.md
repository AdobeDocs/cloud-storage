---
title: Cloud Storage and Collaboration Concepts
description: Adobe Cloud Storage and Collaboration API cloud storage concepts
keywords:
  - Adobe Cloud Storage and Collaboration
  - Adobe Cloud Storage and Collaboration API
  - Adobe Cloud Storage
  - Creative production workflows
  - Adobe workflows
  - Cloud Storage endpoints
  - Cloud Storage authentication and authorization
  - Projects
  - Folder
  - File
contributors:
  - https://github.com/michael-hodgson
  - https://github.com/lijumjose

layout: none
---

# Collaboration constructs

The Adobe Cloud Storage and Collaboration API provides programmatic access to constructs designed to support asset management and team collaboration. Each construct serves a unique purpose, providing users with tailored environments for storing assets, organizing workflows, and achieving specific outcomes.

## Project

A project is a durable space that enables teams to organize and collaborate on cloud-based documents and related content. It is a structured location containing related assets, specifically organized to achieve well-defined objectives within a set timeframe, helping teams stay focused on deliverables.
Projects can contain both files and folders.

## Folder

A folder is a container for organizing and storing content. You can use folders to store files or nest them together to create a folder hierarchy to keep your assets organized. Folders can contain files as well as other folders.

## File

Files are used to store content in Adobe cloud storage. Files come in three types:

- [Cloud documents:](https://helpx.adobe.com/creative-cloud/apps/work-with-cloud-documents/manage-cloud-storage/cloud-documents-overview.html) These are cloud-native files optimized for web applications. You can open and edit them in compatible apps such as Adobe Express, Adobe Photoshop, Adobe Illustrator, Adobe InDesign, Adobe Fresco, Adobe Aero, and Adobe XD.
- [Brands and Libraries:](https://helpx.adobe.com/creative-cloud/apps/create-and-manage-libraries/create-and-organize-libraries/libraries-overview.html) These collections consist of reusable assets and guidelines for content reuse across documents.
- **Regular binary files:**  These include standard file types used with Adobe applications, such as JPEG, PNG, and MP4 files.

Although most operations within the Cloud Storage and Collaboration API apply uniformly across all file types, certain differences exist:

- Cloud documents cannot be downloaded as they are cloud constructs. Use the [image renditions](../api/specification.md#operation/getFileImageRendition) function to get a flat file representation of a cloud document asset.
- Brands and Libraries cannot be downloaded or used to generate a rendition, as these are container objects.
