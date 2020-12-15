# Demo 1 Parts Unlimited MRP - Infrastructure As Code

### Duration
This demo should take ~ 6 minutes

### Goal
Introduction and high-level overview of the Infrastructure As Code DevOps principal in Parts Unlimited MRP. 

### Prerequisites
* An active Visual Studio Team Services (VSTS) account
* [SETUP - Parts Unlimited MRP App Continuous Deployment on Multiple Environment with Visual Studio Team Services - Hosted](../../SETUP_Continuous-Deployment/Setup.md)
* An Active Azure subscription
* [Azure CLI v0.10.5 or later](https://azure.microsoft.com/en-us/documentation/articles/xplat-cli-install/)
* [Visual Studio CODE](http://code.visualstudio.com/)
* An Internet connection

# Demo

## Deploy the ARM template

> Azure CLI is a multi-platform, open source command line interface for Azure.

0. Open a command prompt at the root of your repository, ie: ./PartsUnlimitedMRP

0. Login to your Azure account 
 ```
 azure login
 ```
 [Follow instruction from the command prompt]

 [After providing your credentials, the command returns the result of your login.]

        ...
        info:    login command OK


0. If you have multiple subscriptions, provide the subscription id you wish to use for deployment.

 ```
 azure account set <YourSubscriptionNameOrId>
 ```

3. Switch to Azure Resource Manager module

 ```
 azure config mode arm
 ```
 You will receive confirmation of the new mode.

        info:     New mode is arm

0. Create a new resource group.

 ```
 azure group create -n PartsUnlimitedMRP -l "West US"
 ```
 A summary of the new resource group is returned.

        info:    Executing command group create
        + Getting resource group PartsUnlimitedMRP
        + Creating resource group PartsUnlimitedMRP
        info:    Created resource group PartsUnlimitedMRP
        data:    Id:                  /subscriptions/####/resourceGroups/PartsUnlimitedMRP
        data:    Name:                PartsUnlimitedMRP
        data:    Location:            westus
        data:    Provisioning State:  Succeeded
        data:    Tags:
        data:
        info:    group create command OK

0. Create new deployment (You will want to change the 'mrpDnsNameForPublicIP' parameter value to ensure the dns name is unique)

 ```
 azure group deployment create -g PartsUnlimitedMRP -f .\docs\Sessions\SETUP_Continuous-Deployment\env\ContinuousDeploymentPartsUnlimitedMRP.json -p "{\"mrpAdminUsername\": {\"value\":\"mrp-admin\"}, \"mrpAdminPassword\": {\"value\":\"k34$asdKJWUjg213!!!#32asd\"}, \"mrpDnsNameForPublicIP\": {\"value\":\"Linux-mrp-demo-iac\"}}" -m incremental -v
 ```

 A verbose summary of the new resource group is returned.

        info:    Executing command group deployment create
        verbose: Initializing template configurations and parameters
        verbose: Creating a deployment
        info:    Created template deployment "ContinuousDeploymentPartsUnlimitedMRP"
        verbose: Waiting for deployment to complete
        verbose:
        ...
        ...
        info:    group deployment create command OK

 [ !!! Explain that this can take up to 10 minutes, so we won't wait and we'll switch to VSCode 
    to examine what is this ARM template that we are using]
 
## Explain what an Azure Resource Manager Template is

> Visual Studio CODE is a multi-platform, open source editor.

0. Launch Visual Studio code

0. Open Your repository folder (the one you unzipped and pushed to VSTS during setup)
 
 ![](<media/open_repo_folder_vscode.png>)

    > What can be considered *Infrastructure As Code* in Parts Unlimited MRP? It is the Azure Resource Manager (ARM) template.
    
    > Let's take a look at it...

0. Navigate to the file `./docs/Sessions/SETUP_Continuous-Deployment/env/ContinuousDeploymentPartsUnlimitedMRP.json`

    > An Azure Resource Manager (ARM) template is a Javascript Object Notation (JSON) file (basically a text file).
    JSON has a smaller footprint than XML, which is wayyyyy too verbose and a bit out of date.
    JSON in its nature is very static and there is not much you can do about it

 ![](<media/arm_template_closed_sections.png>)

    > There are **5 important sections** in an ARM template
     
    > The **$schema** (a required element that provides the location of the file that describes the version of the template language)
        along with a required element that provides the version of the template.
        The schema reference is used by intelligent JSON clients to determine the schema that 
        is applicable to the JSON file and to provide additional functionality such as autocomplete and intellisense. 

    > **parameters** (optional elements that define values that are passed in when the template is executed).
        The value for the Parameters key is an array of parameter objects that
        represents the dynamic inputs for the JSON template.
        Each of the parameter objects has a name that is used to pass values in to the template at runtime and is referenced within the JSON itself in other sections.

    > **variables** (optional elements that define the values that are used when the template is executed).
        You can have static variables (static string) or dynamic variables (because they have a template expression).

    > **resources** (a required element that defines the resources that are deployed or updated in a resource group).
        This is where you'll have your focus most of the time. 

    > **outputs** (optional elements that define the values that are outputed when template is executed).
        In our case we output the `InstrumentationKey` of Application Insights for this environment. 

0. Expand the **parameters** section

 ![](<media/arm_template_parameters.png>)

 > There are **4 parameters** in the template
 >
 > **mrpAdminUsername** is a *string* parameter that corresponds to the username of the administrator account for the Linux virtual machine.
 >
 > **mrpAdminPassword** is a *securestring* parameter that corresponds to the password of the administrator account for the Linux virtual machine.
 > The *securestring* type is treated differently by the ARM engine. It will travel encrypted and will not show up in logs.
 > 
 > **mrpDnsNameForPublicIP** is a *string* parameter that corresponds to a prefix that will be used in the construction of the dns name for the Linux virtual machine.
 >
 > **CustomScriptsParentPath** is a *string* parameter that corresponds to public URI of the container from which the customization script for the 
 > Linux virtual machine will be taken.
 >

[ !! If you want to skip a minute in demo, pass over the variables section]

0. Collapse the **parameters** section & expand the **variables** section

 ![](<media/arm_template_variables.png>)

 > There are many variables in the template, but let's just describe a few
 >
 > **mrpImageOffer** is a *string* variable that set the virtual machine image to use to Ubuntu.
 >
 > **mrpImageSku** is a *string* variable that set the version for the virtual machine image.
 > 
 > **mrpVmSize** is a *string* variable that corresponds to the size of the virtual machine, in this case, a *Standard_D1_V2*.
 >

0. Collapse the **variables** section & expand the **resources** section

 ![](<media/arm_template_resources.png>)

 > There are many resources that will be created in this template, you can quickly see that 
 > we have a storage account that will be used for the virtual machine disk and also for VM diagnostic.
 >
 > There is also the public IP that will be used by the virtual machine

0. Scroll down to the resource type `Microsoft.Compute/virtualMachines` or search for `virtualMachines`

 ![](<media/arm_template_resources_vm.png>)

 > A resource has mandatory properties...
 > 
 > **apiVersion** instruct the ARM engine which version of the Resource Provider will be used
 >
 > **type** defines which resource type you are describing, in this case the resource provider is `Microsoft.Compute`
 > and the resource type is a virtualMachine
 >
 > **name** a unique name for the resource in Azure; for a virtual machine this is not to be confused with the DNS name which is set in the Public IP resource  
 >
 > **location** is the Azure region in which the resource will be provisioned.
 >
 > **properties** is the property bag for the resource type. Each resource type will have a different bag of properties for you to set.

 > The virtual machine will be created but will be pretty much raw and we need a way to customize it 
 > so it can be used in the Parts Unlimited MRP context. To do so, in the template we'll rely on a child resource of the virtual machine,
 > the `CustomScriptForLinux` extension.

0. Scroll down to the resource type `Microsoft.Compute/virtualMachines/extensions` or search for `CustomScriptForLinux`

 ![](<media/arm_template_resources_customscript.png>)

 > Virtual Machines extensions are special resources that install and execute an agent on the virtual machine. 
 > The one used in this template will execute a shell script through bash.
 >
 > You can provide a `fileUris` property to instruct the agent which files need to be downloaded onto the virtual machine before executing the command
 > set in the `commandToExecute` property. In our case all commands are in-lined. It is a bit more difficult to read, so we'll show a more eye friendly
 > version of the commands in an external file.

0. Using the navigation pane (CTRL+B is hidden) open the file `install_mrp_dependencies.sh`

 ![](<media/arm_template_customscript.png>)
 
 > As you can see this will perform a number of installs on the Linux virtual Machine; install Java JDK, Java runtime, mongodb, tomcat, etc...

## Show the resources in the Azure portal
 
0. Browse into your ResourceGroup in the Azure portal [https://portal.azure.com](https://portal.azure.com) to show resources have been created       

 ![](<media/browse_rg_azure_portal.png>)

> The JSON template can be put inside source control & a version of it can be tracked to follow the evolution of your project. This is why this kind of approach is called
> *Infrastruture As Code* 

## Teardown
None.  

## Complementary information

* [DevOps Fundamentals](https://channel9.msdn.com/Series/DevOps-Fundamentals)
