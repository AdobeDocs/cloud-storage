---
title: Cloud Storage Collaboration Concepts - Adobe Cloud Storage and Collaboration API
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

# Collaboration Constructs

The Adobe Cloud Storage and Collaboration API provides programmatic access to a set of constructs specifically designed to support asset management and team collaboration. Each construct serves a unique purpose, providing users with tailored environments for storing assets, organizing workflows, and achieving specific outcomes.

## Project

A project is a durable space that enables teams to organize and collaborate on cloud-based documents and related content. It is a structured location containing related assets, specifically organized to achieve well-defined objectives within a set timeframe, helping teams stay focused on deliverables.
Projects can contain both files and folders.

## Folder

A folder is a container to organize and store content. You can use folders to store files, or you can nest them together to create a folder hierarchy to keep your assets organized. Folders can contain files as well as other folders.

## File

Files are used to store content in Adobe cloud storage. Files come in three types;

- [Cloud documents](https://helpx.adobe.com/creative-cloud/help/about-cloud-documents.html) are cloud-native files optimized for web applications. You can open and edit in compatible apps such as Adobe Express, Adobe Photoshop, Adobe Illustrator, Adobe InDesign, Adobe Fresco, Adobe Aero, and Adobe XD
- Brands and [Libraries](https://helpx.adobe.com/creative-cloud/help/libraries.html) are collections of reusable assets and guidelines to aid content reuse across documents
- Regular, binary files such as those ued in conjunction with Adobe applications, such as jpeg, png, and mp4 files.

While most Cloud Storage and Collaboration API operations are the same across all types of files, there are a few differences:

- Cloud documents cannot be downloaded as they are cloud constructs. Use the file `renditions` function to get a flat file representation of a cloud document asset
- Brands and Libraries cannot be downloaded or used to generate a rendtion as these are container objects
