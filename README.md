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

Onfido’s Studio orchestration platform offers a more dynamic and flexible way of guiding applicants through the required steps of a verification journey. The best way of doing this is to make use out of workflow_links.

To do this, start by [creating a workflow link](TBC) using the Onfido API. At a minimum, you will need to provide a workflow ID for a valid, active Studio workflow:

```http request
POST https://api.<region>.onfido.com/v4/workflow_links
Content-Type: application/json
Authorization: Token token=<YOUR API KEY>

{
    "workflow_id": "<WORKFLOW_ID>",
}
```

You can then use the workflow link `id` returned in API response payload:

```json
{
    "id": "<ID>",
    "applicant_id": "<APPLICANT_ID>",
    "expires": "2022-06-26T12:56:25.547952",
    "url": "https://studio.eu.onfido.app/l/<ID>",
    "workflow_id": "<WORKFLOW_ID>"
}
```

to bootstrap the SDK, using the following javascript code:

```js
window.handle = Onfido.init({
    workflowLinkId: "<ID>"
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
            workflowLinkId: "<WORKFLOW_LINK_ID>",
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


