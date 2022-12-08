# Framed Web SDK

The Onfido [Web SDK](https://github.com/onfido/onfido-sdk-ui) is a library that can be bootstrapped with a set of parameters in order to render a user interface to ensure the correct capture and upload of applicant information, such as identity documents, selfie photos and videos.

The Web SDK runs in the context of a 3rd party, meaning that customers are responsible for updating to the latest versions as they are released.  

The framed Web SDK is an idea for Onfido to host a small javascript that bootstraps the Web SDK in an iframe. Because this iframe is hosted by Onfido, we can deliver the latest version of the SDK to customers without any required changes on their side.
`

# How to use it
## Embedding and initialization

1. Embed the client javascript, `<script src="https://assets.onfido.com/web-sdk-client/client.js"></script>` in your html page. Do not deliver this javascript yourself, as we ensure it's always up to date and delivered fast via a CDN.
2. Call `const handle = Onfido.init(parameter)` with an object to initialise the SDK. All supported parameters are documented [here](https://github.com/onfido/onfido-sdk-ui#6-initialize-the-sdk).

## Custom css

As we run the SDK within an iframe to verify your applicants, you can style the content in the iframe to suit your needs by passing custom css via the `css` parameter.

## Tearing down

To tear down the sdk, use the handle object returned from the `Onfido.init` method and call `handle.tearDown()` to remove the SDK from your webpage.

# Modes of operation
## Static
         
This is the standard method for Web SDK [integrations](https://github.com/onfido/onfido-sdk-ui#6-initialize-the-sdk), where the parameters and steps are pre-assigned and there is no workflow evaluated behind the scenes. Use this static mode if you do not have access to [Onfido Studio](https://developers.onfido.com/guide/onfido-studio-product).

At a minimum, you will need to pass an [SDK token](https://github.com/onfido/onfido-sdk-ui#3-generate-an-sdk-token) in the `token` parameter object:

```js
window.handle = Onfido.init({
    token: "<YOUR SDK TOKEN>" // https://github.com/onfido/onfido-sdk-ui#3-generate-an-sdk-token
});
```
                                                                                                
## Workflow

Onfido’s Studio orchestration platform offers a more dynamic and flexible way of guiding applicants through the required steps of a verification journey. The best way of doing this is to configure the SDK with a workflow run ID.

To do this, start by [creating a workflow run](https://documentation.onfido.com/#create-workflow-run) using the Onfido API. At a minimum, you will need to provide an applicant ID and a workflow ID for a valid, active Studio workflow:

```http request
POST /v3.5/workflow_runs HTTP/1.1
Host: api.eu.onfido.com
Authorization: Token token=<YOUR_API_TOKEN>
Content-Type: application/json

{
  "workflow_id": "<WORKFLOW_ID>",
  "applicant_id": "<APPLICANT_ID"
}
```

You can then use the workflow run `id` returned API response payload:

```json
{
  "id": "<WORKFLOW_RUN_ID>",
  "applicant_id": "<APPLICANT_ID>",
  "workflow_id": "<WORKFLOW_ID>",
  "workflow_version_id": 11,
  "status": "approved",
  "dashboard_url":"https://dashboard.onfido.com/results/<WORKFLOW_RUN_ID>"
  "output": {"prop1": "val_1", "prop2": 10}, 
  "reasons": ["reason_1", "reason_2"],
  "error": null,
  "created_at": "2022-06-28T15:39:42Z",
  "updated_at": "2022-07-28T15:40:42Z",
  "link": {
      "completed_redirect_url": "https://example.onfido.com",
      "expired_redirect_url": "https://example.onfido.com",
      "expires_at": "2022-10-17T14:40:50Z",
      "language": "en_US",
      "url": "https://eu.onfido.app/l/<WORKFLOW_RUN_ID>"
  },
}
```

to bootstrap the SDK, using the following javascript code:

```js
window.handle = Onfido.init({
    workflowRunId: "<WORKFLOW_RUN_ID>"
});
```

# Example implementations
## Static

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
</head>
<body>
    <script src="https://assets.onfido.com/web-sdk-client/client.js"></script>
    <script>
        window.handle = Onfido.init({
            token: "<YOUR SDK TOKEN>",
            onComplete: (outcome) => {
                console.log("complete", outcome)
            },
            
            steps: ["welcome", "face", "complete"],

            css: `
            html {
                background: repeating-linear-gradient(45deg, #606dbc, #606dbc 10px, #465298 10px, #465298 20px);
            }
            `
        })
    </script>
</body>
</html>
```

## Workflow

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
</head>
<body>
    <script src="https://assets.onfido.com/web-sdk-client/client.js"></script>
    <script>
        window.handle = Onfido.init({
            workflowRunId: "<WORKFLOW_RUN_ID>",
            region: "EU", // make sure to provide region "EU", "US", "CA"
            onComplete: (a) => {
              console.log(a)
            },
            `
        })
    </script>
</body>
</html>
```


