# Getting Started

Welcome to your new BookShop Sample project.

It contains these folders and files, following the SAP CAP recommended project layout:

File / Folder | Purpose
---------|----------
`app/` | content for UI frontends go here
`db/` | your domain models and data go here
`srv/` | your service models and code go here
`package.json` | project metadata and configuration
`readme.md` | this getting started guide


## Run the application
- Open a new terminal and run  `cds watch`
- ( in VSCode simply choose _**Terminal** > Run Task > cds watch_ )
- Start adding content, e.g. a [db/schema.cds](db/schema.cds), ...


## Learn more...

Learn more at https://cap.cloud.sap/docs/get-started/

## Manual Deployment
This project can be deployed via a mta-file.
The deployment descriptor is mta.yaml.
Documentation can be found at [Deploy using MTA](https://cap.cloud.sap/docs/advanced/deploy-to-cloud#deploy-using-mta)

The mta file can be generated via the command
 ```
 cds add mta
 ```

 The mtar archive can be built using
 ```
 mbt build
 ```
 This command will look for the mta.yaml for the application definition.

 The mtar can be deployed on the SAP Cloud Platform using 
 ```
 cf deploy
 ```

 ### Note:
 * to build the mtar you need to have `make` installed for you os. For windows you can use `chocolatey.
 ```
 choco install make
 ```
 * for deployment the `cf`-cli needs the MTA pluginn. Install using
 ```
 cf add-plugin-repo CF-Community https://plugins.cloudfoundry.org
 cf install-plugin multiapps
 ```

## Docker Images
SAP provides docker images to build and deploy the app. These images can be called via Azure DevOps.
The docker images can be found on dockerhub.
<!-- * Deprecated - MTA Builder : [ppiper/mta-archive-builder](https://hub.docker.com/r/ppiper/mta-archive-builder). For more information, have a look at [Multitarget Application Archive Builder](https://github.com/SAP/devops-docker-mta-archive-builder) -->
* Cloud MTA Build Tool (mbt) : [devxci/mbtci](https://hub.docker.com/r/devxci/mbtci). For more information, have a look at [Welcome to the Cloud MTA Build Tool (mbt)](https://sap.github.io/cloud-mta-build-tool/) and [Cloud MTA Build Tool overview](https://github.com/SAP/cloud-mta-build-tool)
* Cloud Foundry Command Line Interface (cli): [ppiper/cf-cli](https://hub.docker.com/r/ppiper/cf-cli). For more information, have a look at [Cloud Foundry Command Line Interface](https://github.com/SAP/devops-docker-cf-cli)

Before stepping to Azure DevOps, let's run the docker images manually. This can be done on a Linux vm with docker installed.
* Clone the github directory & change to the root directory of you application
```
git clone https://github.com/bdelangh/my-bookshop-mta.git
cd my-bookshop-mta
```
* run the docker image to build the pipeline. The folder containing the project needs to be mounted into the image at `/project`.
```
sudo docker run -it --rm -v "$(pwd):/project" devxci/mbtci:latest mbt build -p=cf
```
* the mtar file should now be available at ../mta_archives
* Deploy to SAP Cloud Platform using the following command :
```
sudo docker run --rm -v "${PWD}":/project ppiper/cf-cli /bin/bash -c "cf login -u 'bart.delanghe@microsoft.com' -p 'MicroBelg&7' -a 'https://api.cf.us10.hana.ondemand.com' -o '68f6a2bbtrial' -s 'dev' && cd /project && cf deploy mta_archives/my-bookshop2_1.0.0.mtar"
```
* You can now test the bookshop app on the SAP Cloud Platform

## Integration with Azure DevOps
Building and deploying the application can also be done via Azure DevOps. 

* Login to Azure DevOps
* Create a new project in Azure DevOps
* Create a new pipeline
    * point to the github with the source code as the source
    * select a 'starter pipeline'
    * Insert the following code as pipeline
```yaml

```
Notes :
* System.DefaultWorkingDirectory : The local path on the agent where your source code files are downloaded.
* Pipeline.Workspace : The local path on the agent where all folders for a given build pipeline are created.

* Create Pipeline variables
    * CF-USER = SAP Cloud Platform username
    * CF-PASSWORD = SAP Cloud Platform password
    * CF-API = login url (eg. https://api.cf.us10.hana.ondemand.com)
    * CF-ORG = Organization 
    * CF-SPACE = Space (eg. dev)
* Run the pipeline


## Documentation
* [How to Use Project “Piper” Docker Images for CI/CD with Azure DevOps](https://blogs.sap.com/2019/10/24/how-to-use-project-piper-docker-images-for-cicd-with-azure-devops/)
