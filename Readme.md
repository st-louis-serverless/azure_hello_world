# Read Me



## Labs for November 6, 2018 Meetup

This month's meeting is to gently introduce you to developing serverless apps on Azure.  There is one core lab and 
two _take home_ labs.

**Prerequsites**

1. You need to have created an Azure account, either a paid or trial account, and/or a Sandbox Learning account
2. Obviously, you'll need Internet access

### Lab 1: Create an Azure function via the Azure Portal

In this lab, you will create an Azure Function that is triggered by an HTTP request. The lab is designed to allow 
you to get your feet wet creating an Azure Function without drowning you in a lot of complexity installing and 
configure the CLI, Core Tools, .NET Core, etc.

1. Survey the landscape 
	- Log onto your account in the Portal
	- Visit the Services page to take in the breadth of services
	- Enter Function App in the search field
	
2. Create a new Resource Group
	- Name it what you wish, but make it meaningful and globally unique
	
3. Create a new Function App
	- Name it what you wish, but make it meaningful and globally unique
	
4. Add a Function
	- Name it what you wish, but make it meaningful and URL-friendly
	- We'll use JavaScript
			 
5. Use this code for the function:
	```
	module.exports = async function (context, req) {
        context.log('JavaScript HTTP trigger function processed a request.');
    
        context.log.info({'req.query': req.query})
        context.log.info({'req.body': req.body})
    
        const min = req.query.min || (req.body && req.body.min) ? parseInt((req.query.min || req.body.min)) : 1
        const max = req.query.max || (req.body && req.body.max) ? parseInt((req.query.max || req.body.max)) : 100
        const randomVal = Math.floor(Math.random() * (max - min + 1) + min)
    
        const result = {
            min: min,
            max: max,
            randomVal: randomVal 
        }
        
        context.res = {
            status: 200, // Defaults to 200, but included here for illustration
            body: result
        }
        
    }
    ```
	
6. Review the code, taking note of
	- The use of the context object for logging. In AWS Lambda, we just log to the console like normal
	
7. Click on the Integration node
	- Note the allowed methods are GET and POST
	
8. Test the function in the Portal
    - On the right margin, you'll see the _Test_ tab
    - Click it to open the Test pane
    - Change the HTTP method to GET
    - Add two parameters and values
        
        ```
        min     50
        max     75
        ```
    - Click the Run button
        - Note the result in the output panel
        - Note the console output
        
    - Now change the HTTP method to POST
    - Add this in the body panel:
    ```
    {
        "min": 50,
        "max": 75
    }
    ```
    - Run the test again and observe the output
    
9. Let's test the function from a browser
	- Get the URL from the Function panel by clicking on the link [</> Get function URL]()
	- Enter the copied url in a browser followed by `?min=50&max=75`
	- If using Chrome, you'll see the JSOn response in the main panel of the browser
	
10. Optional: test POST through **curl**, **httpie**, or **Postman** 	 

### Lab 2 (Take home): Create an Azure function via the CLI

One way to automate the development and provisioning of your functions is through CLI scripts.

*Prerequisites*
Install [Azure CLI](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli?view=azure-cli-latest) and the 
Install [Azure Core Tools](https://www.npmjs.com/package/azure-functions-core-tools) to develop and run the functions 

1. [Read through these detailed instructions](https://docs.microsoft.com/en-us/azure/azure-functions/functions-create-first-azure-function-azure-cli)
2. Now, go through each step
3. After verifying the function works, change the code (maybe using what was in Lab 1) and test again

### Lab 3 (Take home): Use Gradle to package a function, run it locally, and deploy it to Azure

*Prerequisites*
Install [Azure CLI](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli?view=azure-cli-latest) and the 
Install [Azure Core Tools](https://www.npmjs.com/package/azure-functions-core-tools) to develop and run the functions

1. [Clone this Github project](https://github.com/stlserverless/azure_hello_world) 

2. Review the project files

This is a very simple project just for illustrating the Azure Functions Gradle Plugin.  The plugin is not yet in Maven
so you'll need to clone the repo, build the plugin, and publish it to your local Maven repo in *.m2/repository*.

3. Install the Azure Gradle Functions Plugin

Visit that [Github repo](https://github.com/lenala/azure-gradle-plugins) and read the Readme on the Functions plugin. 

For this project, you only need to build, package and publish locally the nested `azure-functions-gradle-plugin` 
project.

**Note 1:** I found one unit test failed, but just @Ignore'd it and the plugin seemed to work okay. It's definiely a 
WIP.

**Note 2:** There are multiple authentication options. I chose the one that created an encrypted authentication file 
on the local file system. Authentication is discussed in the Gradle plugin ReadMe and in Azure documentation.

Once that Gradle plugin is published locally, you should be able to build and run this project.  

4. Read through the `build.gradle` file, making adjustments as necessary to ensure the resource group and function 
app names will be globally unique. 

5. Read through the Java code

6. Execute the Gradle **azureFunctionsPackage** task in the Other group

7. Execute the Gradle **azureFunctionsRun** in the Other group to run your function locally
	- Test a POST request against the localhost address
	- Note: Only a plain test value need be supplied in the body 

8. Execute the Gradle **azureFunctionsDeployment**  task in the Other group

9. Go into the Azure Portal and review the artifacts created there under the resource group name you specified in `build.gradle`

10. Test the function via the public URL

11. Test a short develop/package/deploy cycle
	- Change the code locally
	- Run the build task
	- Run the function package task
	- Test the function locally
	- Run the function deploy task
	- Test it remotely
	
### Cleanup

Unless you'll be using all these labs in the future, you may consider deleting the create resource groups. This
will delete the storage, the functions, configuration files, and other artifacts. This eliminates the risk of 
getting unexpected usage and charges. 
	