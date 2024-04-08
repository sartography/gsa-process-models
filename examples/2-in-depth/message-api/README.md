# BPMN as an API
This BPMN Process can be executed remotely.  This can be useful in some of the following cases.

## Use Cases

### Take control of the user interactions
* Useful if you have an existing product or application that you want to use to start and/or communicate with running workflows.
* If you have a web form in your website, and want that form to start or update a running workflow.

### Allow workflows to listen for external events
* If you have "web hooks" within something like GitHub and you want those to trigger or update a workflow.

### Do It Yourself Micro Services
* Imagine your sales team wants to manage their own promotion codes.  They can build an API endpoint that accepts a promotion code and returns a price list for that code.  Your main sales web site can call this API that sales maintains to get updates on the fly.   

## How to use this Demo

1. **Generate an API Key** - click on your account icon in the top right corner and select "API Keys" - give the key a name, and save the value for the next step.
2. **Make the API call** - you can do this from the command line, or with a tool like Post Man. Here are the critical values you will need:

    * The BASE_URL - for this website, that is https://api.spiffdemo.org
    * The [MESSAGE_NAME] your BPMN process is listening for  - in this case "popcorn"
    * [YOUR_API_KEY] that you generated in Step 1 above,
    * The [DATA] you want to post, in this case it will be one of ...
       * '{"size":"large"}' 
       * '{"size":"medium"}' 
       * '{"size":"small"}' 

## Making the call using Curl:  

Here is the Template:
```bash
curl \
'[BASE URL]/v1.0/messages/[MESSAGE_NAME]?execution_mode=synchronous' \
-H 'Spiffworkflow-Api-Key: [YOUR KEY]' \
-H 'Content-Type: application/json' \
-X POST -d '[DATA]'

```
