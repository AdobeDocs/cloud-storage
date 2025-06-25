---
title: Quickstart guide for Adobe Cloud Storage and Collaboration API
description: An overview of the Adobe Cloud Storage and Collaboration API
keywords:
 - Adobe Cloud Storage and Collaboration
 - Adobe Cloud Storage and Collaboration API
 - Adobe Cloud Storage
 - Creative production workflows
 - Adobe workflows
 - Cloud Storage endpoints
 - Cloud Storage authentication and autorization
 - Cloud Storage quickstart
 - Code Samples
contributors:
- https://github.com/michael-hodgson
layout: none
---

# NOTE: All code is currently based on RAPI, Projects, and Invitations services. Replace when the 3rd party APIs are available

## Quickstart Guide

Automating content workflows with Cloud Storage and Collaboration APIs

![BlueRaccoon](../images/BlueRaccoon.jpg)

## Prerequisites

### Credentials

If you don't already have a Cloud Storage and Collaboration **Client ID** and **Client Secret**, retrieve them from your [Adobe Developer Console project](https://developer.adobe.com/developer-console/docs/guides/services/services-add-api-oauth-s2s/#api-overview) before reading further. **Securely store these credentials and never expose them in client-side or public code**. This tutorial uses a Server-To-Server credential, but Cloud Storage and Collaboration API also supports User credentials.

### Setting up your environment

Before we begin this tutorial, run the following in a secure terminal:

```bash
mkdir cloud-storage-api-getstarted-tutorial
cd cloud-storage-api-getstarted-tutorial
npm init --y
npm install axios qs
touch index.js
```

Depending on your learning style, you may prefer to walk through this tutorial step-by-step or jump immediately to the [full source code](#full-example).

## Retrieve an Access Token

Open a secure terminal and export your **Client ID** and **Client Secret** as environment variables so that your later commands can access them:

```bash
export CLOUD_STORAGE_CLIENT_ID=yourClientIdAsdf123
export CLOUD_STORAGE_CLIENT_SECRET=yourClientSecretAsdf123
```

Generate an access token:

<CodeBlock slots="heading, code" repeat="2" languages="bash, JavaScript" />

#### cURL

```bash
curl --location 'https://ims-na1.adobelogin.com/ims/token/v3' \
--header 'Content-Type: application/x-www-form-urlencoded' \
--data-urlencode 'grant_type=client_credentials' \
--data-urlencode "client_id=$CLOUD_STORAGE_CLIENT_ID" \
--data-urlencode "client_secret=$CLOUD_STORAGE_CLIENT_SECRET" \
--data-urlencode 'scope=openid, AdobeID, offline_access, creative_sdk'
```

#### JavaScript

```js
// Generate access token
async function retrieveAccessToken() {
 
    const CLIENT_ID = process.env.CLOUD_STORAGE_CLIENT_ID;
    const CLIENT_SECRET = process.env.CLOUD_STORAGE_CLIENT_SECRET;
    const SCOPES = "openid, AdobeID, offline_access, creative_sdk";  // TODO: Update with new scopes
 
    console.log('Generating Access Token');
    const data = qs.stringify({
        grant_type: 'client_credentials',
        client_id: CLIENT_ID,
        client_secret: CLIENT_SECRET,
        scope: SCOPES,
    });
 
    const config = {
        method: 'post',
        url: 'https://ims-na1-stg1.adobelogin.com/ims/token/v2', // TODO: Update with production URL
        headers: { 'Content-Type': 'application/x-www-form-urlencoded' },
        data: data,
    };
 
    try {
        const response = await axios.request(config);
        console.log('Access Token Retrieved');
        return response.data.access_token;
    } catch (error) {
        console.error('Error retrieving access token:', error.response.data);
 
    }
}
```

The response will look like this:

```bash
{"access_token":"yourAccessTokenAsdf123","token_type":"bearer","expires_in":86399}
```

Export this access token so that the next script can conveniently access it:

```bash
export FIREFLY_SERVICES_ACCESS_TOKEN=yourAccessTokenAsdf123
```

## List projects

Now that you have an access token you can begin to work with Adobe Creative Cloud projects. A project is a durable shared space within your organization. For more information about Adobe Creative Cloud Projects see: [Creative Cloud projects overview](https://helpx.adobe.com/creative-cloud/help/projects-overview.html).

You can start by getting a list of the existing projects. It is important to note that only projects that the requesting user or Technical Account has access to will be included in the response.

```js
//Get a list of existing projects
async function getProjects(accessToken) {
    {
        console.log('Getting project list.');
        const headers = {
            'Content-Type': 'application/json',
            'x-api-key': CLIENT_ID,
            Authorization: `Bearer ${accessToken}`,
        }

        const config = {
            method: 'GET',
            url: STORAGE_ENDPOINT + '/projects', // TODO: replace with production URLs 
            headers: headers,
        };

        try {
            const response = await axios(config);
            const projectList = response.data;  // TODO: Update for 3p API response
            return projectList;
        } catch (error) {
            console.error('Error during create project:', error.response?.data || error.message);
        }
    }
}
```

The response will look like this:

```js
//TODO: Add response from 3p APIs
```

## Create a project

Next you will create a new Adobe cloud storage project so you have a place where the creative production team can store and share content.

Start by making a request to create a new project with the name you have selected

```js
//Create a new project
async function createProject(accessToken, projectName) {
    console.log('Creating new project');
    const headers = {
        'Content-Type': 'application/json',
        'x-api-key': CLIENT_ID,
        Authorization: `Bearer ${accessToken}`,
    }
    const data = {
        "name": projectName,
    };

    const config = {
        method: 'post',
        url: STORAGE_ENDPOINT + '/projects',
        headers: headers,
        data: data,
    }

    try {
        const response = await axios(config);
        const projectId = response.data["assetId"];
        return projectId;
    } catch (error) {
        if (error.response.status == 409) {
            try {
                //Project already exists with that name. Get the id for it
                return await getExistingProjectId(accessToken, projectName);
            } catch (error) {
                //if the project exists, but it isn't in the list of projects, then the user does not have access to it.
                throw new Error('Project named: ' + projectName + ' exists, but you do not have access to it.')
            }
        } else {
            throw new Error('Error during create project');
        }
    }
}
```

Notice there is a check if the create project request throws a 409 error (Conflict). This happens if there is already a project with the same name. In that case code invokes a function to get the asset id of the existing project.

For security reasons, if the requestor doesn't have access permissions to the existing project they won't be able to retrive the project's asset id and error will be thrown.

The function to get the asset id for an existing project starts by getting a list of existing projects, then checks for a matching name.

```js
//get an existing project's asset id
async function getExistingProjectId(accessToken, projectName) {
    const pl = await getProjects(accessToken);
    const projectList = pl.items;

    //find the matching project name in the list of all projects
    const filteredArrayValues = projectList.filter(item => item.name === projectName)
    if (filteredArrayValues.length == 0) {
        throw new Error("Project name not found: " + projectName);
    } else {
        return filteredArrayValues[0].assetId;
    }
}
```

The response for creating a new project will look like this:

```js
//TODO: Add response from 3p APIs
```

## Get info about a project

We can retrieve information about a project that is created using either the APIs, or through Adobe Creative Cloud applications such as Adobe Express, Photoshop Web, or Adobe.com using the Cloud Storage and Collaboration APIs

```js
//get project info
async function getProjectInfo(accessToken, projectId) {
    console.log('Getting project metadata');
 
    const headers = {
        'Content-Type': 'application/hal+json',
        'x-api-key': CLIENT_ID,
        'X-ACP-ADMIN-PRIVILEGES': '58E31C3567F435780A49421C@AdobeOrg', //TODO: should nto be needed for the 3P APIs
        Authorization: `Bearer ${accessToken}`,
    };
 
    const config = {
        method: 'get',
        url: 'https://ccprojects-stage.adobe.io/api/v3/projects/' + projectId, // TODO: replace with production URLs
        headers: headers,
    };
 
    try {
        const response = await axios(config);
        const projectMetadata = response.data;  // TODO: Update for 3p API response
        return projectMetadata;
    } catch (error) {
        console.error('Error during get project info: ', error.response?.data || error.message);
    }
}
```

The response will look like this:

```js
//TODO: Add response from 3p APIs
```

## Invite a user to a project

Now that we have a project, we want to invite other users so we can all collaborate on the assets within the project. Collaborators can be assigned and **edit** or **comment** permissions, which apply to all items within a project. You will invite a user to the project using their email address.

```js
//set the user role for a project
async function inviteUser(accessToken, projectId, userEmail, userRole) {
    console.log('Adding user to the project');
 
    const headers = {
        'Content-Type': 'application/json',
        'x-api-key': CLIENT_ID,
        Authorization: `Bearer ${accessToken}`,
    };
 
    // TODO: replace with 3p API format (should be much simpler)
    const userId = "mailto:" + userEmail;
    const data = {
        "inviterLabel": "Service Label",
        "recipients": [
            {
                "canComment": true,
                "canShare": true,
                "recipient": userId,
                "role": userRole
            }
        ],
        "configuration": {
            "acceptanceRequired": false,
            "useMounts": false
        },
        "notification": {
            "ans": {},
            "email": {
                "sharing": {
                    "templateName": "dummy_template",
                    "locale": "EN_US"
                }
            },
            "external": true,
            "parameters": {
                "customString": {
                    "assetType": "project"
                },
                "message": "",
                "targetUrl": "https://www.stage.adobe.com/files/projects/" + projectId + "?openFrom=copy-link"
            }
        }
    };
 
    const config = {
        method: 'post',
        //url: 'https://invitations-stage.adobe.io/api/v4/share/' + projectId + "?mode=direct_acl", // TODO: replace with production URLs
        url: 'https://invitations-stage.adobe.io/api/v4/share/' + projectId,
        params: { "mode": "direct_acl" },
        headers: headers,
        data: data,
    };
 
    try {
        const response = await axios(config);
        const inviteResponse = response.data;  // TODO: Update for 3p API response
        return inviteResponse;
    } catch (error) {
        console.error('Error during invite user: ', error.response?.data || error.message);
    }
}
```

The response will look like this:

```js
//TODO: Add response from 3p APIs
```

## Get project sharing info

We can use the API to see which users have access to the project and their roles

```js
//get sharing info
async function getSharing(accessToken, projectId) {
 
    console.log('Getting project sharing data');
 
    const headers = {
        'x-api-key': CLIENT_ID,
        Authorization: `Bearer ${accessToken}`,
    };
 
    const config = {
        method: 'get',
        url: 'https://invitations-stage.adobe.io/api/v4/share/' + projectId, // TODO: replace with production URLs
        headers: headers,
    };
 
    try {
        const response = await axios(config);
        const projectSharingData = response.data;  // TODO: Update for 3p API response
        return projectSharingData;
    } catch (error) {
        console.error('Error during get project sharing: ', error.response?.data || error.message);
    }
 
}
```

The response will look like this:

```js
//TODO: Add response from 3p APIs
```

## Upload a file to a project

Next we will use the Cloud Storage and Collaboration APIs to upload a file to the project. In this case we will upload a JPG file from the same location as our application (_cloud-storage-api-getstarted-tutorial_).

```js
//upload a file
async function uploadFile(accessToken, projectId, fileName) {
    console.log("Reading local file");
 
    //read the image file from the local directory
    var imgPath = __dirname + "/" + fileName;
    var fileData;
    const fsFile = await fs.readFile(imgPath)
        .then((data) => {
            fileData = Buffer.from(data);
        })
        .catch((error) => {
            console.error('Error reading file:', error);
        });
 
    console.log("Uploading file to the project");
    const headers = {
        'x-api-key': CLIENT_ID,
        'Content-Type': "image/jpg",
        Authorization: `Bearer ${accessToken}`,
    };
 
    const config = {
        method: 'post',
        url: 'https://platform-cs-stage-va6.adobe.io/content/storage/id/' + projectId + "/:create", // TODO: replace with production URLs
        params: { "path": fileName },
        headers: headers,
        data: fileData
    };
 
    try {
        const response = await axios(config);
        const fileUploadResponse = response.headers["asset-id"];  // TODO: Update for 3p API response
        return fileUploadResponse;
 
    } catch (error) {
        console.error('Error during file upload: ', error.response?.data || error.message);
        if (error.response.status == 409) {
            console.log('File already exists');
            return error.response.data["repo:assetId"];
        }
    }
}
```

The response will look like this:

```js
//TODO: Add response from 3p APIs
```

## List the contents of a project

Now that we have a file in the project, we can get a list of the projects contents. The contents will include all files and folders that are created by through the API, or that are added to the project using Creative Cloud applications.

```js
//get a list of the files/folders in a project
async function getProjectContents(accessToken, projectId) {
    console.log("Getting a list of the project's contents");
 
    const headers = {
        'Content-Type': 'application/json',
        'x-api-key': CLIENT_ID,
        Authorization: `Bearer ${accessToken}`,
    }
 
    const config = {
        method: 'get',
        url: 'https://platform-cs-stage-va6.adobe.io/content/storage/id/' + projectId, // TODO: replace with production URLs
        headers: headers,
    };
 
 
    try {
        const response = await axios(config);
        const projectContents = response.data.children;  // TODO: Update for 3p API response
        return projectContents;
    } catch (error) {
        console.error('Error during get project contents: ', error.response?.data || error.message);
    }
}
```

The response will look like this:

```js
//TODO: Add response from 3p APIs
```

## Get a rendition of a file

We can use the Cloud Storage and Collaboration API to get a rendition of an asset stored in Adobe cloud storage. A rendition is a representation of a file, typically containing less data than the asset from which it is derived. The prototypical example of a rendition is the thumbnail of an image, which is usually a lossy and smaller version of the original image.

```js
//get a file rendition
async function getFileRendition(accessToken, fileId, renditionSize, renditionFileName) {
    console.log("Gettting a rendition of the file");
 
    const headers = {
        'Content-Type': 'image/jpg',
        'x-api-key': CLIENT_ID,
        Authorization: `Bearer ${accessToken}`,
    }
 
    const config = {
        method: 'get',
        url: 'https://platform-cs-stage-va6.adobe.io/rendition/id/' + fileId, // TODO: replace with production URLs
        params: { "size": renditionSize },
        headers: headers,
        responseType: 'stream',
    };
 
    try {
        const finishedDownload = ut.promisify(stream.finished);
        const renditionFilePath = __dirname + "/" + renditionFileName;
        const writer = ws.createWriteStream(renditionFilePath);
        const response = await axios(config);
 
        response.data.pipe(writer);
        await finishedDownload(writer);
        console.log("Rendition written: " + renditionFilePath);
 
    } catch (error) {
        console.error('Error during get file rendition: ', error.response?.data || error.message);
    }
 
};
```

The response will look like this:

```js
//TODO: Add response from 3p APIs
```

In this example, the resulting rendition is saved to the same location as our application (cloud-storage-api-getstarted-tutorial) in a file named rendition.jpg. You can open the file and see that the image size is reduced.

![rendition](../images/rendition.jpg)

## Download a file

You can also download the file, in its original size, from Adobe cloud storage.

```js
//Download a file
async function downloadFile(accessToken, fileId, downloadFileName) {
    console.log("Downloading the file");
 
    const headers = {
        'Content-Type': 'image/jpg',
        'x-api-key': CLIENT_ID,
        Authorization: `Bearer ${accessToken}`,
    }
 
    const config = {
        method: 'get',
        url: 'https://platform-cs-stage-va6.adobe.io/content/storage/id/' + fileId, // TODO: replace with production URLs
        headers: headers,
        responseType: 'stream',
    };
 
    try {
        const finishedDownload = ut.promisify(stream.finished);
        const downloadFilePath = __dirname + "/" + downloadFileName;
        const writer = ws.createWriteStream(downloadFilePath);
        const response = await axios(config);
 
        response.data.pipe(writer);
        await finishedDownload(writer);
        console.log("File downloaded: " + downloadFilePath);
 
    } catch (error) {
        console.error('Error during download file ', error.response?.data || error.message);
    }
}

```

The response will look like this:

```js
//TODO: Add response from 3p APIs
```

In this example, the resulting rendition is saved to the same location as our application (_cloud-storage-api-getstarted-tutorial_) in a file named _download.jpg_. You can open the file and see that it is the same as the uploaded image.

## See project in UI

You can use the [Adobe Creative Clound Home](https://www.adobe.com/home) web application to see the project you created in this tutorial.

- Open a browser and navigate to the [Adobe Creative Cloud Home](https://www.adobe.com/home) web application
- Sign in as the user that you invited to the project in the **[Invite a user to a project](#invite-a-user-to-a-project)** step.
- You should see a red dot in the Notifications icon (bell icon in the upper right of the screen). Click on the bell icon to see a list of notifications.
- Click on the notification about the invite to the project. This will take you to the project

You can also navigate to the projects diretly using the Creative Cloud Home UI:

- Click on the _Files_ icon
- Choose _Projects_
- You should see the project that you created earlier in the _Projects_ panel. Click on it to view the contents
- The contnts of the project will be shown, including the file you uploaded through the API.

  ===TODO: Add screen shots===

## Full example

```js
/*
Sample code for Cloud Storage and Collaboration API Quickstart

Uses the Cloud Storage and Collaboration API (3rd party ACPC API)

TODO: Update for production. All URLs are stage.
*/

const axios = require('axios');
const qs = require('qs');
const fs = require('fs').promises;
const ws = require('fs')
var http = require('http');
const stream = require('stream');
const ut = require('util');

const roles = {
    Editor: "edit",
    Commenter: "comment"
};

// TODO: Replace with environment variables */
const CLIENT_ID = "1d0cd59658184f84a0ecd387aac4e329";
const CLIENT_SECRET = "s8e-6pu0ZU_pDsq3tM-JDMat3g87YN3pXjyX";
const SCOPES = "openid, AdobeID, creative_sdk, cloud_storage_collab_api";  // TODO: Update with new scopes

const STORAGE_ENDPOINT = "https://cloudstorage-stage.adobe.io/v1";

(async () => {

    try {
        const accessToken = await retrieveAccessToken();

        //Get a list of existing projects
        const projectList = await getProjects(accessToken);
        // console.log('Project List:');
        // console.log(JSON.stringify(projectList));

        //Create a new Project
        var projectName = "Blue Raccoon";
        const projectId = await createProject(accessToken, projectName);
        console.log("Project id: " + projectId);

        //Get info about a project
        var projectInfo = await getProjectInfo(accessToken, projectId);
        console.log("Project info:");
        console.log(JSON.stringify(projectInfo));

        //Invite a user to a project
        /* TODO: wait until the API is available
        var userEmail = "mhodgson+US+Free+VISA+1@adobetest.com"
        var userRole = roles.Editor;
        var inviteUserResponse = await inviteUser(accessToken, projectId, userEmail, userRole);
        console.log("User invite info:");
        console.log(JSON.stringify(inviteUserResponse));
        */

        //Get the project's sharing info (all users)
        var projectSharing = await getSharing(accessToken, projectId);
        console.log("Project sharing info:");
        console.log(JSON.stringify(projectSharing));

        //Upload an image file to a project
        const fileName = "BlueRaccoon.jpg";
        /* TODO: update when API is available
       const fileId = await uploadFile(accessToken, projectId, fileName);
       */
        const fileId = 'urn:aaid:sc:US:d419ffaa-3703-38a6-b021-0cd01727f165';  //TODO: remove when we can get the id from the upload

        console.log("File id:");
        console.log(fileId);

        //List project contents
        const projectContents = await getProjectContents(accessToken, projectId);
        console.log(JSON.stringify(projectContents));

        //Get a rendition of a file
        /* TODO: not implemented yet
        const renditionSize = 100;
        const renditionFileName = "rendition.jpg";
        const renditionFileData = await getFileRendition(accessToken, fileId, renditionSize, renditionFileName);
        */

        //Download a file
        const downloadFileName = "download.jpg";
        const fileDownloadData = await downloadFile(accessToken, fileId, downloadFileName);

    } catch {
        console.error('Error executing quickstart. ', error.response?.data || error.message);

    }

})();

// Generate access token
async function retrieveAccessToken() {
    console.log('Generating Access Token');

    //TODO: Remeber to get the access token from env variables. Currently hard coded until the S2S auth is available.
   
    const data = qs.stringify({
        grant_type: 'client_credentials',
        client_id: CLIENT_ID,
        client_secret: CLIENT_SECRET,
        scope: SCOPES,

    });

    const config = {
        method: 'post',
        url: 'https://ims-na1-stg1.adobelogin.com/ims/token/v2', // TODO: Update with production URL
        headers: { 'Content-Type': 'application/x-www-form-urlencoded' },
        data: data,
    };

    try {
        const response = await axios.request(config);
        console.log('Access Token Retrieved');
        return response.data.access_token;
    } catch (error) {
        console.error('Error retrieving access token:', error.response.data);
    }
}

//Get a list of existing projects
async function getProjects(accessToken) {
    {
        console.log('Getting project list.');
        const headers = {
            'Content-Type': 'application/json',
            'x-api-key': CLIENT_ID,
            Authorization: `Bearer ${accessToken}`,
        }

        const config = {
            method: 'GET',
            url: STORAGE_ENDPOINT + '/projects', // TODO: replace with production URLs 
            headers: headers,
        };

        try {
            const response = await axios(config);
            const projectList = response.data;  // TODO: Update for 3p API response
            return projectList;
        } catch (error) {
            console.error('Error during create project:', error.response?.data || error.message);
        }
    }
}

//Create a new project
async function createProject(accessToken, projectName) {
    console.log('Creating new project');
    const headers = {
        'Content-Type': 'application/json',
        'x-api-key': CLIENT_ID,
        Authorization: `Bearer ${accessToken}`,
    }
    const data = {
        "name": projectName,
    };

    const config = {
        method: 'post',
        url: STORAGE_ENDPOINT + '/projects',
        headers: headers,
        data: data,
    }

    try {
        const response = await axios(config);
        const projectId = response.data["assetId"];
        return projectId;
    } catch (error) {
        if (error.response.status == 409) {
            try {
                //Project already exists with that name. Get the id for it
                return await getExistingProjectId(accessToken, projectName);
            } catch (error) {
                //if the project exists, but it isn't in the list of projects, then the user does not have access to it.
                throw new Error('Project named: ' + projectName + ' exists, but you do not have access to it.')
            }
        } else {
            throw new Error('Error during create project');
        }
    }
}

//get an existing project's asset id
async function getExistingProjectId(accessToken, projectName) {
    const pl = await getProjects(accessToken);
    const projectList = pl.items;

    //find the matching project name in the list of all projects
    const filteredArrayValues = projectList.filter(item => item.name === projectName)
    if (filteredArrayValues.length == 0) {
        throw new Error("Project name not found: " + projectName);
    } else {
        return filteredArrayValues[0].assetId;
    }
}

//get project info
async function getProjectInfo(accessToken, projectId) {
    console.log('Getting project metadata for project: ' + projectId);

    const headers = {
        'x-api-key': CLIENT_ID,
        Authorization: `Bearer ${accessToken}`,
    };

    const config = {
        method: 'get',
        url: STORAGE_ENDPOINT + '/projects/' + projectId,
        headers: headers,
    };

    try {
        const response = await axios(config);
        const projectMetadata = response.data;  // TODO: Update for 3p API response
        return projectMetadata;
    } catch (error) {
        console.error('Error during get project info: ', error.response?.data || error.message);
    }
}

//set the user role for a project
async function inviteUser(accessToken, projectId, userEmail, userRole) {
    console.log('Adding user to the project');

    const headers = {
        'Content-Type': 'application/json',
        'x-api-key': CLIENT_ID,
        Authorization: `Bearer ${accessToken}`,
    };

    //TODO: not working  - API still being developed
    //Tried:
    // email address: mhodgson+US+Free+VISA+1@adobetest.com
    // IMS ID from UMAPI: 65841C2D67F4CBFF0A494010@580c1c3067f4358949403d.e


    const data = {
        "direct": {
            "additions": [
                {
                    "principalId": "mhodgson+US+Free+VISA+1@adobetest.com",  //TODO - get this part working
                    "principalType": "user",
                    "role": "edit"
                }
            ]
        }
    };

    const config = {
        method: 'PATCH',
        url: STORAGE_ENDPOINT + '/projects/' + projectId + '/permissions',
        headers: headers,
        data: data,
    };

    try {
        const response = await axios(config);
        const inviteResponse = response.data;  // TODO: Update for 3p API response
        return inviteResponse;
    } catch (error) {
        console.error('Error during invite user: ', error.response?.data || error.message);
    }
}

//get project sharing info
async function getSharing(accessToken, projectId) {
    console.log('Getting project sharing data');

    const headers = {
        'x-api-key': CLIENT_ID,
        Authorization: `Bearer ${accessToken}`,
    };

    const config = {
        method: 'GET',
        url: STORAGE_ENDPOINT + '/projects/' + projectId + '/permissions',
        headers: headers,
    };

    try {
        const response = await axios(config);
        const projectSharingData = response.data;  // TODO: Update for 3p API response
        return projectSharingData;
    } catch (error) {
        console.error('Error getting project sharing info: ', error.response?.data || error.message);
    }
}

//upload a file
async function uploadFile(accessToken, projectId, fileName) {
    console.log("Reading local file");

    //read the image file from the local directory
    var imgPath = __dirname + "/" + fileName;
    var fileData;
    const fsFile = await fs.readFile(imgPath)
        .then((data) => {
            fileData = Buffer.from(data);
        })
        .catch((error) => {
            console.error('Error reading file:', error);
        });

    //direct upload the file to the project

    console.log("Uploading file to the project");
    const headers = {
        'x-api-key': CLIENT_ID,
        Authorization: `Bearer ${accessToken}`,
        'Content-Type': 'multipart/form-data',
    };

    //TODO: based on proposal: https://wiki.corp.adobe.com/display/CA/Proposal%3A+%5B3rd+Party+APIs%5D+Managing+Upload+and+Download+of+File+Data
    //TODO: not working yet.   


    // The direct upload function takes a multipart/form-data requet body that consists of two parts:
    //  Part 1 is application/json data that instruct the API where to put the file, and what name to call it
    const uploadInfo = {
        "parentId": projectId,
        "name": fileName
    }
    const body = new FormData();
    body.append('metadata', uploadInfo);

    // Part 2 is the file to upload
    body.append('media', fileData);  //"image/jpg",   

    const config = {
        method: 'post',
        url: STORAGE_ENDPOINT + "/files/content", // TODO: replace with production URLs
        headers: headers,
        data: body,
    };

    try {
        const response = await axios(config);
        const fileUploadResponse = response.headers["asset-id"];  // TODO: Update for 3p API response
        return fileUploadResponse;

    } catch (error) {
        console.error('Error during file upload: ', error.response?.data || error.message);
        if (error.response.status == 409) {
            console.log('File already exists');
            return error.response.data["repo:assetId"];
        }
    }

}

//get a list of the files/folders in a project
async function getProjectContents(accessToken, projectId) {
    console.log("Getting a list of the project's contents");

    const headers = {
        'Content-Type': 'application/json',
        'x-api-key': CLIENT_ID,
        Authorization: `Bearer ${accessToken}`,
    }

    const config = {
        method: 'get',
        url: STORAGE_ENDPOINT + '/projects/' + projectId + '/list', // 
        headers: headers,
    };

    try {
        const response = await axios(config);
        const projectContents = response.data.items;  // TODO: Update for 3p API response
        return projectContents;
    } catch (error) {
        console.error('Error during get project contents: ', error.response?.data || error.message);
    }
}

//TODO: not implemented yet
//get a file rendition
async function getFileRendition(accessToken, fileId, renditionSize, renditionFileName) {
    console.log("Gettting a rendition of the file");

    const headers = {
        'Content-Type': 'image/jpg',
        'x-api-key': CLIENT_ID,
        Authorization: `Bearer ${accessToken}`,
    }

    const config = {
        method: 'get',
        url: STORAGE_ENDPOINT + '/files/' + fileId + '/rendition',
        headers: headers,
        responseType: 'stream',
    };

    try {
        const finishedDownload = ut.promisify(stream.finished);
        const renditionFilePath = __dirname + "/" + renditionFileName;
        const writer = ws.createWriteStream(renditionFilePath);
        const response = await axios(config);

        response.data.pipe(writer);
        await finishedDownload(writer);
        console.log("Rendition written: " + renditionFilePath);

    } catch (error) {
        console.error('Error during get file rendition: ', error.response?.data || error.message);
    }

};

// TODO: not working as expected. Gives a presigned URL to the file to download - and not the file itself
//Download a file
async function downloadFile(accessToken, fileId, downloadFileName) {
    console.log("Downloading the file");

    const headers = {
        'Content-Type': 'image/jpg',
        'x-api-key': CLIENT_ID,
        Authorization: `Bearer ${accessToken}`,
    }

    const config = {
        method: 'get',
        url: STORAGE_ENDPOINT + '/files/' + fileId + '/download',
        headers: headers,
        //responseType: 'stream',   //TODO - reinstate when the download returns a file and not a presigned URL
    };
    /* TODO - reinstate when the download returns a file and not a presigned URL
        try {
            const finishedDownload = ut.promisify(stream.finished);
            const downloadFilePath = __dirname + "/" + downloadFileName;
            const writer = ws.createWriteStream(downloadFilePath);
            const response = await axios(config);
    
            response.data.pipe(writer);
            await finishedDownload(writer);
            console.log("File downloaded: " + downloadFilePath);
    
        } catch (error) {
            console.error('Error during download file ', error.response?.data || error.message);
        }
    */

    //TODO - this version of the code gets the presigned url. is it as its expected to work?
    try {
        const response = await axios(config);
        const presignedURL = response.data.url;  // TODO: Update for 3p API response
        await downloadFromPresignedURL(presignedURL, downloadFileName)
    } catch (error) {
        console.error('Error during file download - presigned URL version: ', error.response?.data || error.message);
    }

}

//downloads a file from a presigned URL
async function downloadFromPresignedURL(presignedURL, downloadFileName) {
    const headers = {
        'Content-Type': 'image/jpg',
    }
    const config = {
        method: 'get',
        url: presignedURL,
        headers: headers,
        responseType: 'stream',  
    };

    try {
            const finishedDownload = ut.promisify(stream.finished);
            const downloadFilePath = __dirname + "/" + downloadFileName;
            const writer = ws.createWriteStream(downloadFilePath);
            const response = await axios(config);
    
            response.data.pipe(writer);
            await finishedDownload(writer);
            console.log("File downloaded: " + downloadFilePath);
    
        } catch (error) {
            console.error('Error during download file ', error.response?.data || error.message);
        }

}
```

## Deepen your understanding

This quick start guide provides only a sample of what the Cloud Storage and Collaboration APIs can do. Please see the [API Reference](./api-reference.md) for details on the available functions and options the Cloud Storage and Collaboration APIs provides.
