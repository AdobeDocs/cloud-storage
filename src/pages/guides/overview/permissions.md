---
title: Roles and Permissions - Adobe Cloud Storage and Collaboration API
description: Adobe Cloud Storage and Collaboration API roles and permissions
keywords:
  - Adobe Cloud Storage and Collaboration
  - Adobe Cloud Storage and Collaboration API
  - Adobe Cloud Storage
  - Creative production workflows
  - Adobe workflows
  - Cloud Storage endpoints
  - Cloud Storage autorization
  - User roles
  - Access Control
  - Permissions
  - User group
contributors:
  - https://github.com/michael-hodgson
  - https://github.com/lijumjose
layout: none
---

# Roles and Permissions

Adobe cloud storage includes an access control system that manages access to projects, folders, and files. Authorization is granted based on roles:

| Permission                           | Administrator | Creator | Edit | Comment |
|:-------------------------------------|:-------------:|:-------:|:----:|:-------:|
| Rename a project                     |       ✅       |    ✅    |      |         |
| Discard a project                    |       ✅       |    ✅    |      |         |
| View files and folders               |       ✅       |    ✅    |  ✅   |    ✅    |
| Edit files                           |       ✅       |    ✅    |  ✅   |         |
| Create and add files, create folders |       ✅       |    ✅    |  ✅   |         |
| Set roles                            |       ✅       |    ✅    |  ✅   |         |

## Assigning Roles

### Administrators

Users and groups can ony be assigned to an administrator role using the [Adobe Admin Console](https://helpx.adobe.com/enterprise/using/admin-roles.html).
Note that both System Administrators and Storage Administrators have admin permissions to content in Adobe cloud storage

### Creator

The Creator role is automatically assigned to a user when they create a project.

### Edit and Comment

The Edit and Comment roles can be assigned to a user or group in two ways:

1. By inviting others as collaborators using an Adobe UI, such as Creative Cloud Home. See [Share Projects](https://helpx.adobe.com/creative-cloud/help/share-project.html) for more information
2. Using the **Adobe Cloud Storage and Collaboration API** to set the user/group's permission to either _edit_ or _comment_

## Setting roles with the API

The [Project permissions API](https://developer.adobe.com/cloud-storage/guides/api/#operation/patchProjectPermissions) takes a JSON document that defines the changes in a project's permissions. The document has three sections (JSON objects):

1. additions: grants a role to one or more principals
2. updates: changes the role for one or more principals
3. deletions: removes the role for one or more principals

The sections are optional, and you only need to included the ones that are necessary for the changes you want to make to the project's permissions.

Each section will contain an array of objects that define the who you are changing the permissions for, and their new role.

### Additions

Granting access to a project is done by putting an entry into the _additions_ section of the request. Each entry consists of three properties:

- **type** - the type of principal. There are 3 types of principals:
  - **user** - an individual user
  - **group** - a named group of users. See [Manage user groups](https://helpx.adobe.com/enterprise/using/user-groups.html)
  - **predefined** - one of the special [predefined principals](#predefined-principals)
- **recipient** - the principal who you are giving access
  - The recipient value for _user_ types is the prefix _mailto:_ followed by the user's email address
  - The recipient value for _group_ or _predefined_ types is the prefix _name:_ followed by the name of the group or predefined principal
- **role** - the role they will be assigned. Possible values are edit and comment. See [Roles and Permissions](#roles-and-permissions)

For example, the following invites Bob Smith to be an editor, sets all members of the Graphic Design group to be editors, and allow anyone in the organization to comment on the files in a project

```bash
{
  "direct": {
    "additions": [
      {
        "recipient": "mailto:bob-smith@mycompay.com",
        "type": "user",
        "role": "edit"
      },
      {
        "recipient": "name:Graphic Design",
        "type": "group",
        "role": "edit"
      },
      {
        "recipient": "name:_everybody",
        "type": "predefined",
        "role": "comment"
      }
    ]
  }
}
```

### Updates

Changing existing permissions is done using the _updates_ section of the request. Each updates entry consists of three properties:

- **type** - the type of principal. Same as with [additions](#additions).
- **id** - the unique identifier for a user, group, or predefined principal. The _id_ is not the same as the additions _recipient_ property. It is a unique identifier assigned by the system when a permission is granted.

  - You can find a user or group's _id_ using the [GET permissions](https://developer.adobe.com/cloud-storage/guides/api/#operation/getProjectPermissions) call for the project.

  For example the following _GET permissions_ response provides the _id_ properties for the _\_everybody_ predefined principal, as well as two users. One who has accepted the invitation, and one who's invitation is pending.

  ```json
  {
  "direct": [
    {
      "type": "predefined",
      "id": "orgEverybody",
      "name": "_everybody",
      "role": "comment"
    },
    {
      "type": "user",
      "id": "C1D71E08680BCA8C0A49420A@c1651e08680bc694494118.e",
      "role": "edit",
      "email": "alice-gupta@mycompany.com"
    }
  ],
  "pending": [
    {
      "email": "bob-smith@mycompay.com",
      "role": "edit",
      "created": "2025-07-09T17:13:11.588Z",
      "id": "mailto:bob-smith@mycompay.com"
    }
  ]
}
```

* **role** - the role they will be assigned. Same as with [additions](#additions).

For example, the following request updates the permissions of two users to the *comment* role. The first user has accepted the invitation and therefore their id is a unique GUID. The second user has not yet accepted the invitation, so their id is still their email address.

```bash
{
"direct": {
  "updates": [
    {
      "id": "C1D71E08680BCA8C0A49420A@c1651e08680bc694494118.e",
      "type": "user",
      "role": "comment"
    },
    {
      "id": "mailto:bob-smith@mycompay.com",
      "type": "user",
      "role": "comment"
    }
  ]
}
}
````

### Deletions

You can remove a user, group, or predefined principal's access to a project using the deletions section of a request. Each deletions entry consists of two properties:

- **type** - the type of principal. Same as with [additions](#additions).
- **id** - the unique identifier for a user, group, or predefined principal. Same as with [updates](#updates).

For example, the following request deletes the permissions of two users. The first user has accepted the invitation and therefore their id is a unique GUID. The second user has not yet accepted the invitation, so their id is still their email address.

```bash
{
  "direct": {
  "deletions": [
  {
    "id": "C1D71E08680BCA8C0A49420A@c1651e08680bc694494118.e",
    "type": "user"
    },
    {
      "id": "mailto:bob-smith@mycompay.com",
      "type": "user"
    }
    ]
  }
}
```

### Predefined principals

There are two special principals:

- **\_everybody** - includes all users that are [members of the organization](https://helpx.adobe.com/enterprise/using/manage-directory-users.html)
- **authenticated** - includes all users that have signed in, whether they are members of the organization or not.

### Summary

| Principal Type | Additions recipient              | Updates and Deletion id   | Notes                                                                               |
| -------------- | -------------------------------- | ------------------------- | ----------------------------------------------------------------------------------- |
| user           | mailto:{user's email address}    | {user id}                 | Indicates a specific user                                                           |
| group          | name:{user group name}           | {group id}                | Indicates a [user goup](https://helpx.adobe.com/enterprise/using/user-groups.html) |
| predefined     | name:{predefined principal name} | {predifined principal id} | Indicates a special [predefined principal](#predefined-principals)                  |
