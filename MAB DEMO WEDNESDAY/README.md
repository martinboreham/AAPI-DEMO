# Integrate Control-M with Gitlab CI/CD pipeline

This example shows how to include Control-M to a CI/CD pipeline using the Automation API CLI.

This example consists of the following:
* __sample.json:__ Sample file holding job definitions
* __DeployDescriptorStaging.json:__ DeployDescriptor file which updates the sample.json with environment specific values for the staging environment
* __DeployDescriptorMaster.json:__ DeployDescriptor file which updates the sample.json with environment specific values for the production environment
* __.gitlab-ci.yml:__ Gitlab yml file that describes the jobs in the pipeline.


__Note: We will use only 1 Control-M environment. Using the DeployDescriptor, we will update the folder name to simulate a staging and production environment__

# Get your hands dirty:

In order to run this lab, we need to setup a few things:

## Workbench
If not already done, please download and install Oracle Virtual Box and Workbench.
- You can get Oracle virtual box by opening in your explorer: \\aus-bmcdba-01.bmc.com\Library\VirtualBox
- You can download workbench here (this is the temporary link provided by Tany) : [Workbench](https://s3-us-west-2.amazonaws.com/controlm-appdev/temp/workbench-9.18.1.ova)
- From the Oracle VM VirtualBox Manager, go to File > Import Appliance and select the workbench.ova file that you downloaded to your computer and wait for the import process to complete.
- From the left pane, select workbench and click Start to run the appliance. 

## Automation API
If not already done, please install the Automatio API CLI. The full installation guide can be found [here](https://docs.bmc.com/docs/automation-api/9181/installation-784100973.html)
In short
- Install Node.js version 4.x or later. You can download nodejs ![here](https://nodejs.org/en/download/)
- Install npm version 3.x or later. npm is usually installed together with Node.js.
- Download the Automation API CLI from your local workbench: ![https://localhost:8443/automation-api/ctm-cli.tgz](https://localhost:8443/automation-api/ctm-cli.tgz)
- Install the Automation API CLI by running:
    ```npm -g install ctm-cli.tgz```

## Eclipse
For this scenario, we will use Eclipse as IDE. 

NOTE: 
Eclipse requires Java 1.7 or higher. 
___Please note that if you install the 64bit package for Eclipse, your java must also be the 64bit version. If you already have a 32 bit version installed, please install the 32 bit package for Eclipse and visa versa___

- If you don’t have java yet, you can get it [here](https://www.oracle.com/technetwork/java/javase/downloads/jre8-downloads-2133155.html)
- Download and install Eclipse for Java EE developers from [here](https://www.eclipse.org/downloads/packages/release/2018-09/r/eclipse-ide-java-ee-developers)

- At the first launch, you will see this messaeg. You can mark the checkbox to avoid this in the future:

![Elipse3](/README/images/eclipse3.png)
 


# Great, we have the components. Let's let them work together

## First step is to Fork the Lab 6 repository to your own version.
On the Lab-6 project, press fork in the upper bar.

![Fork](/README/images/fork.png)

## Set pipeline variables:

For this lab you would need to set the following variables in the Gitlab pipeline:

![Variables](/README/images/variables.png)

* __STAGING_ENDPOINT:__ This variable should hold the Automation API endpoint for the staging environment. We use https://ctmem.tlvlab.ctm.bmc.com:8443/automation-api
* __STAGING_USER:__ Provide your ldap user name
* __STAGING_PASSWORD:__ Provide your ldap password

For the production environment, we set the variables with the SAME values as above (we will use only 1 control-m environment for this lab):
* __PRODUCTION_ENDPOINT__
* __PRODUCTION_USER__
* __PRODUCTION_PASSWORD__

## Connect Eclipse to your environment
The next step is to connect Eclipse to the repository in Gitlab:

- Start Eclpse and close the welcome screen- 

![Eclipse4](/README/images/eclipse4.png)

- First, Copy the url of your forked repository. You can use the copy button at the top of the project home screen:

![Copy](/README/images/copy.png)

- Next, make sure the Git windows are enabled. In the menu bar, go to Window --> Show View --> Other
 ![Egit1](/README/images/egit_view1.png)

- Select ```Git Repositories``` and ```Git Staging```

 ![Egit2](/README/images/egit_view2.png)
 
 - Rearrage the staging window to the right side. You can customize the layout. We won't use The Tasks and Outline in this scenario so feel free to close them to clean up the layout.

![Eclipse5](/README/images/eclipse5.png) 

- In the Git Repositories view, press the clone button (3rd icon from the left)

 ![Clone1](/README/images/clone1.png)
 
 - The copied URL is already filled in. Provide your ldap credentials and continue:

 ![Clone2](/README/images/clone2.png)

- Unselect the master branch and press next. We will only work from the staging branch. We will use gitlab to promote changes from staging to the master branch.

![Clone3](/README/images/clone3.png)

- Press finish

![Clone4](/README/images/clone4.png)

- Now import the repsitory as project in eclipse:

![Clone5](/README/images/clone5.png)

- Select "Import as general project" and press next

![Clone6](/README/images/clone6.png)

- Provide a project name or keep the default 

![Clone7](/README/images/clone7.png) 

## Connect Eclipse to the Automation API and Workbench

We are almost done, but first we need to define the Automation API as external build/run tool. 

- From the Menu bar, go to Run --> External Tools --> External Tools Configurations...

- Than add the following Automation API services as external runner:


|Name                              |Location                         |Arguments                                                                                                                                            |
|----------------------------------|---------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------|
|Build in Control-M                |C:\Windows\System32\cmd.exe      |/c ctm build "${resource_loc}"                                                             |
|Run in Control-M                  |C:\Windows\System32\cmd.exe      |/c ctm run "${resource_loc}" -i                                                            |
|Transform in Control-M Staging    |C:\Windows\System32\cmd.exe      |/c ctm deploy transform "${resource_loc}" "${project_loc}"\DeployDescriptorStaging.json    |
|Transform in Control-M Production |C:\Windows\System32\cmd.exe      |/c ctm deploy transform "${resource_loc}" "${project_loc}"\DeployDescriptorProduction.json |


- For each line, add an entry, and provide the information for fields marked with a red arrow.

![ext_run2](/README/images/ext_run2.png) 

- For each entry, mark them as favorite in the common tab:

![ext_run3](/README/images/ext_run3.png) 

## Update the DeployDescriptor Files

Open the DeoployDescriptorStaging.json and DeployDescriptorMaster.json files from the package explorer and update
- The run as user
- Add the first 3 letters of your username (like on our demo environment)

![run3](/README/images/run3.png) 


# Give it a spin

## Run the sample.json file against the workbench

Select the sample.json file in the project repository, and press Run with Control-M

![run1](/README/images/run1.png) 

Your browser should open automatically with the following screen.
![run1](/README/images/run2.png) 

## Commit the changes to the updated DeployDescriptor File

You have already updated "artifacts" of this repository by updating the deploy descriptor file. Now let's commit and Push these changes to your repository in Gitlab.

- Move the DeoployDescriptorStaging.json and DeployDescriptorMaster.json from the unstaged changes en move them to Staged chages. (you can leave .project)
- Also provide a commit message

![run4](/README/images/run4.png)

- Press Commit and Push. The result should look like;

![run5](/README/images/run5.png)

- Now go to your repository in Gitlab, and open the pipeline page.
- You now have your first succesfull run:

![run6](/README/images/run6.png)

- Go to the monitoring domain on the lab EM. You should now find your folder. It is in the monitoring domain because we already executed it as part of the CI/CD pipeline.

 ![run7](/README/images/run7.png)

## Let's promote our changes to production

We already applied our changed deploydescriptor files to our staging environment. Now lets promote them to production:

- First, create a merge request:

 ![merge1](/README/images/merge1.png)
 
 - Update the source branch to staging and select your personal Lab-6 project as target (not the shared one). 

![merge2](/README/images/merge2.png)
  
- Press compare branches and continue
- Press assing to me and scroll down to submit the request

 ![merge3](/README/images/merge3.png)
 
- Approve the request by pressing merge

 ![merge4](/README/images/merge4.png)
 
- Go back to your CI/CD pipelines. You should now see one for the master branch

 ![merge5](/README/images/merge5.png)
 
- Go back to the monitoring domain. You will now see the same folder with PRODUCTION in the folder name (to simulate a seperate production environment)

 ![merge6](/README/images/merge6.png)
 
# FREESTYLE!!
 
 Now your CI/CD pipeline is fully set. You can play around and make any change, but i can recommend trying to reproduce the demo scenario from the ilearn video. The scenario is:
 
 - As a developer, you are assinged an issue with a job call ThisOneWillFail. This jobs always runs into an error.
 - First step for you as a developer is to reproduce the issue. Run the sample.json from eclipse to see what is going on.
 - In the workbench gui, open the output of ThisOneWIllFaill. You'll quickly discover the issue.
 - Now go back to eclipse. Change the command for ThisOneWillFail to ```sleep 15```
 - Build the sample.json file and run it again. For demo you might want to show what happens when you make an error. 
 - Now commit the changes to the repository. After the Commit and Push, validate that this job is now fixed on the staging environment
 - Finally, create a merge request. Approve it and check if the change is now also applied on the production environment (same lab enviroment, but folder with PRODUCION in the name)


