## This is a Command Line Tutorial
This example covers the technical details of how to work directly with the underlying SpiffWorkflow API.  It will be most useful to software engineers that may want to integrate SpiffWorkflow into their existing applications. 

### curl
We'll be doing all of this from the command line using [CURL](https://www.geeksforgeeks.org/curl-command-in-linux-with-examples/0) - if you have done much work with API end points this tool should be familiar to you.  It allows you to make direct requests to the SpiffWorkflow website from the command line.

### jq
Not a requirement, but we'll be piping things through jq to make things a little prettier.  

# Step 1: Create an API Key
To get started you will need to click on your account settings in the top right, create an API Key.  Take the generated value (not the name you give it, but the long random string it generates) and put it in a local file so we can easily reference it in the commands below.

something like:
```bash
echo 828e99f8-8437-42b5-854e-a51e2c0a23ad > t 
```
Now we ave a file called "t" that contains our key. This way you can just past many of the commands below.

# Step 2:  Create an instance of this process

```bash
curl -s -X POST \
https://api.spiffdemo.org/v1.0/process-instances/examples:2-in-depth:direct-api-calls \
-H "Spiffworkflow-Api-Key: $(cat t)" \
| jq . 
```

This will return a json structure similar to the following.  You will want to pull out the "id" number at the top.  This is our instance id.  We'll refer to this moving forward.

```json
{
  "id": 5275,
  "process_model_identifier": "examples/2-in-depth/direct-api-calls",
  "process_model_display_name": "Direct API Calls",
  "process_initiator_id": 563,
  "start_in_seconds": 1711577220,
  "end_in_seconds": null,
  "updated_at_in_seconds": 1711577220,
  "created_at_in_seconds": 1711577220,
  "status": "not_started",
  "bpmn_version_control_identifier": "8a6a30ff"
}
```

Go ahead and echo this number into a file as well. 

```bash
echo [THE ID FROM ABOVE YOUR RESULT] > id
```

We should make sure the process starts by calling a run command.  

```bash
curl -s -X POST \                               
https://api.spiffdemo.org/v1.0/process-instances/examples:2-in-depth:direct-api-calls/$(cat id)/run \
-H "Spiffworkflow-Api-Key: $(cat t)" \
| jq .
{
``` 


# Step 3 - Get the ready tasks for the process instance 

``` bash
curl -s \
"https://api.spiffdemo.org/v1.0/tasks?process_instance_id=$(cat id)" \
-H "Spiffworkflow-Api-Key: $(cat t)" \
| jq . 
```
This will return the ready tasks for the process, and will look something like this

```json 
{
  "results": [
    {
      "id": "312caeeb-0656-4461-904c-b900b6301997",
      "form_file_name": "favorite-city-schema.json",
      "task_type": "UserTask",
      "ui_form_file_name": "favorite-city-uischema.json",
      "task_status": "READY",
       [SNIP]
      "assigned_user_group_identifier": null,
      "potential_owner_usernames": "my_key_6_1711569827.370353"
    }
  ],
  }
```
You will want to save this task id as well, so we can reuse that in the next call.  So grab the id that was returned and echo it into a file called "task"

```bash
echo [THE ID FROM YOUR RESULT] > task
```

# Step 4 - Get the Form:
It looks like this task uses the *favorite-city-schema.json* form (see "form_file_name" from above.  Let's fetch that file. 

```bash
curl -s \
https://api.spiffdemo.org/v1.0/process-models/examples:2-in-depth:direct-api-calls/files/favorite-city-schema.json \
-H "Spiffworkflow-Api-Key: $(cat t)" \
 | jq -r .file_contents
```
This will return the JSON schema that needs to be completed ...

```json
{
  "type": "object",
  "required": [
    "favorite_city"
  ],
  "properties": {
    "favorite_city": {
      "type": "string",
      "title": "Please enter the name of your favorite city"
    }
  }
}
```
This response, this JSON Schema, tells us that we we need to supply a favorite_city in order to complete the next user task.  This could of course be far more complicated, this is just a one field schema to illustrate the point.

You could render this form in your own application using the same library we do - [RJSF](https://github.com/rjsf-team/react-jsonschema-form).

# Step 5 - complete the form.

We can submit the form back to the running process using the following curl command. 

```bash
curl -X PUT \
-H 'Content-type: application/json' \
-d '{"favorite_city": "Prague"}' \
"https://api.spiffdemo.org/v1.0/tasks/$(cat id)/$(cat task)" \
-H "Spiffworkflow-Api-Key:  $(cat t)" | jq .
```
This will return details about the what happens next -- in this case the process will complete.  In other cases there may be new tasks to complete.

```json
{
  "id": "70b5877e-a7eb-4692-a49d-36c20c727b9e",
  "name": "EndEvent_1",
  [SNIP]
  "properties": {
    "instructionsForEndUser": "The process instance completed successfully."
  },
  "process_instance_id": 5275,
  "process_model_display_name": "Direct API Calls",
  [SNIP]
}

```


That's it!  Hopefully this has been helpful in describing how to access some of the important functionality via the API - even when you want to complete tasks from individuals. 


