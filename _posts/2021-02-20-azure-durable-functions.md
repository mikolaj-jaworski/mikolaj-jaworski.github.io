---
layout: post
title: Azure Durable Functions - Overview & Setup
subtitle:
tags: [azure, durable functions]
comments: false
---
### Overview 
People who just came across Azure Functions may don't even know that there is another, more powerful version of this service - **Durable Functions**. The main difference is the workload that we can pass to the function. According to the [documentation](https://docs.microsoft.com/pl-pl/azure/azure-functions/functions-scale), for the most common HTTP-triggered functions the maximum time of waiting for response to the request is 230 seconds, which can be not enough for more complex cases. Thanks to [asynchronous pattern](http://dontcodetired.com/blog/post/Understanding-Azure-Durable-Functions-Part-9-The-Asynchronous-HTTP-API-Pattern), **durable functions don't have such time limit**. The second difference (and very useful feature) is **Orchestrator**, which allows us to chain the set of multiple functions and execute them in desired order, which makes them very useful for multi-step data processing. The service cost is related to the workload, but it is considered very cheap.

### Version Settings
If we just set up our working environment (downoaded extensions for VSCode and created resources in Azure) this part should be all right. But sometimes we want to create new function using old code or maybe Microsoft just released new version of Functions. In this case we would like to make sure local and cloud settings are compliant. So we need to go through few points:<br><br>
**Update function version in the Azure**. We can easily check and update function version in Azure Portal (Configuration tab in Function panel) or from VSCode. In the IDE we can find *FUNCTIONS_EXTENSION_VERSION* in *Azure/{your_subscription}/{your_function_app}/Application Settings* and edit the value. <br><br>
**Update your local functions package**. Your function version can be checked by running the function locally:
```bash
func host start
```
To update package we can use [npm](https://www.npmjs.com/package/azure-functions-core-tools) (in this case version ~3):
```bash
npm i -g azure-functions-core-tools@3 --unsafe-perm true
```
Moving on... <br><br>
**Update function's files**. Firstly, we need to open VSCode settings: *.vscode/settings.json* and update *azureFunctions.projectRuntime* to desired value (~3). Then in main folder in *host.json* we need to make sure values are correct. The first occurence of *version refers to file structure, so we leave it unchanged. But the *extensionBundle* version should be set to [2.*, 3.0.0):
```json
{
  "version": "2.0",
  "logging": {
    "applicationInsights": {
      "samplingSettings": {
        "isEnabled": true,
        "excludedTypes": "Request"
      }
    }
  },
  "extensionBundle": {
    "id": "Microsoft.Azure.Functions.ExtensionBundle",
    "version": "[2.*, 3.0.0)"
  }
}
``` 
Those 3 steps should be enough for version matching.

### Requirements
File listing all packages required by Function app is stored in *requirements.txt*. This is essential part of the function. When we create virtual environment, we can execute: 
```bash
conda create --name azurefunc python=3.7 --yes
conda activate azurefunc
pip install --upgrade pip
pip install -r requirements.txt
```
**Tip**: I came across some weird errors when my function was using some kind of Azure Storage. For example, when we use Azure Blob, we should include *azure-storage* instead of *azure-storage-blob* in requirements. However, when using Data Lake, we should be more specific mentioning *azure-storage-file-datalake*. It is also worthy to point on specific verion of the package, for example *numpy==1.19.1*.

### Response test
We can check if function works by triggering Orchestrator. One way is to use Python script:
```python
import json
import requests
headers = {"Content-Type": "application/json"}
url = r'http://localhost:7071/api/orchestrators/Orchestrator' 
mydict = {"variable1":{"arg1":1, "arg2"false},
	  "variable2":{"arg1":1, "arg2"false}}
json_string = json.dumps(mydict)
r = requests.post(url, data=json_string, headers = headers)
```

### Usage in Data Factory Pipeline
After deploying function to the Azure, we can set up DataFactory pipeline. 
<br><br>
In **Manage** tab we create connector between DataFactory and Functions. We point on the function of interest using Subscription and validate it using *_master* function key (derived form Function panel). Using *_master* would allow us to execute all functions inside the group, which would be beneficial when creating multi-step pipeline involving several functions (if not chained by Orchestrator). We could use KeyVault for storing the key, which is considered as good practice.
<br><br>
Having connection we can move to **creating pipeline**.
- Linked Service: choose the connector.
- Function Name: *orchestrators/Orchestrator* (if we want to trigger simple http function, we use just name)
- Method: *POST*
- Headers: *Content-Type: application/json*
- Body: *{"variable1":{"arg1":1, "arg2"false}, "variable2":{"arg1":1, "arg2"false}}*

Finally, we **add WebActivity step**, which monitors if function activities are done. We need to set dynamic parameter: *@activity('previous_step_name').output.statusQueryGetUri* with GET method.

That is all for now. The post may be extended in the future.