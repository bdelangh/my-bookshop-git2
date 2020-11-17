# Deploying SAP CAP application to SAP Cloud Platform using Azure DevOps

This example makes use of the SAP CAP BookShop Sample project. This sample uses NodeJS at the application layer and HANA as a database.
The original project can be found at [SAP Cloud CAP Samples](https://github.com/SAP-samples/cloud-cap-samples).

This article describes how this application can be deployed on the SAP Cloud Platform using Azure DevOps. As intermediary steps it also describes how a manual deployment and a deployment using docker can be executed. 

## Test the application locally
It's always a good idea to test the application locally.
- Open a new terminal and run  `cds watch` (from the root directory of you application)
- (in VSCode simply choose _**Terminal** > Run Task > cds watch_ )

## Manual Deployment
To get a feel on the needed steps for deployment, let's first execute a manual deployment.
This project can be deployed via a mta.yaml deployment descriptor.
>Documentation can be found at [Deploy using MTA](https://cap.cloud.sap/docs/advanced/deploy-to-cloud#deploy-using-mta)

The mta file can be generated via the command
 ```
 cds add mta
 ```

 The mtar archive can be build using
 ```
 mbt build
 ```
 This command will look for the mta.yaml for the application definition.

 The mtar can be deployed on the SAP Cloud Platform using 
 ```
 cf deploy
 ```

 >Notes :
 >* to build the mtar you need to have `make` installed for you OS. For windows you can use `chocolatey` for installation.
 >```
 >choco install make
 >```
 >* for deployment to SAP Cloud Platform, the `cf`-cli needs the `MTA plugin`. Install this using
 >```
 >cf add-plugin-repo CF-Community https://plugins.cloudfoundry.org
 >cf install-plugin multiapps
 >```

## Docker Images
SAP provides docker images to build and deploy the app. These images can be called via Azure DevOps.
>Note : using the docker images relieves you from having to install `make`, `cf cli`, ... locally.

The docker images can be found on dockerhub.
<!-- * Deprecated - MTA Builder : [ppiper/mta-archive-builder](https://hub.docker.com/r/ppiper/mta-archive-builder). For more information, have a look at [Multitarget Application Archive Builder](https://github.com/SAP/devops-docker-mta-archive-builder) -->
* Cloud MTA Build Tool (mbt) : [devxci/mbtci](https://hub.docker.com/r/devxci/mbtci). For more information, have a look at [Welcome to the Cloud MTA Build Tool (mbt)](https://sap.github.io/cloud-mta-build-tool/) and [Cloud MTA Build Tool overview](https://github.com/SAP/cloud-mta-build-tool)
* Cloud Foundry Command Line Interface (cli): [ppiper/cf-cli](https://hub.docker.com/r/ppiper/cf-cli). For more information, have a look at [Cloud Foundry Command Line Interface](https://github.com/SAP/devops-docker-cf-cli)

## Deployment using Docker Images
As another intermediary step, let's run the docker images manually. This can be done on a Linux vm with docker installed. (I used an Ubuntu 18.0 vm on Azure.)

The following commands are executed on this docker vm. 
* Clone the github directory & change to the root directory of your application
```
git clone https://github.com/bdelangh/my-bookshop-mta.git
cd my-bookshop-mta
```
* run the docker image to build the mtar archive. The folder containing the application needs to be mounted into the image at `/project`.
```
sudo docker run -it --rm -v "$(pwd):/project" devxci/mbtci:latest mbt build -p=cf
```
* the mtar file should now be available at ../mta_archives
* Deploy to SAP Cloud Platform using the following command :
```
sudo docker run --rm -v "${PWD}":/project ppiper/cf-cli /bin/bash -c "cf login -u 'username' -p 'password' -a 'loginurl' -o 'orgunit' -s 'space' && cd /project && cf deploy mta_archives/my-bookshop2_1.0.0.mtar"
```
Replace `username`, `password`, `cf login url`, `orgunit` and `space` with your respective data.

* You can now test the bookshop app on the SAP Cloud Platform

## Deployment using Azure DevOps
Building and deploying the application can also be done using Azure DevOps.

* Login to Azure DevOps
* Create a new project in Azure DevOps
* Create a new pipeline
    * point to your github with the source code
    * select a 'starter pipeline'
    * Insert the following code as pipeline : [azure-pipelines.yml](azure-pipelines.yml)
* Create Pipeline variables : these variables are used to login to SAP Cloud Platform. 
    * CF-USER = SAP Cloud Platform username
    * CF-PASSWORD = SAP Cloud Platform password
    * CF-API = SAP Cloud Platform login url (eg. https://api.cf.eu10.hana.ondemand.com)
    * CF-ORG = Organization 
    * CF-SPACE = Space (eg. dev)
>Note : In a production environment you might want to store some of these values in a KeyVault.   
* Run the pipeline to deploy the application

<!-- Notes :
* System.DefaultWorkingDirectory : The local path on the agent where your source code files are downloaded.
* Pipeline.Workspace : The local path on the agent where all folders for a given build pipeline are created.
-->


## Documentation
* [How to Use Project “Piper” Docker Images for CI/CD with Azure DevOps](https://blogs.sap.com/2019/10/24/how-to-use-project-piper-docker-images-for-cicd-with-azure-devops/)
