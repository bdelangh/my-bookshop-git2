# Getting Started

Welcome to your new BookShop Sample project.

It contains these folders and files, following our recommended project layout:

File / Folder | Purpose
---------|----------
`app/` | content for UI frontends go here
`db/` | your domain models and data go here
`srv/` | your service models and code go here
`package.json` | project metadata and configuration
`readme.md` | this getting started guide


## Next Steps...

- Open a new terminal and run  `cds watch`
- ( in VSCode simply choose _**Terminal** > Run Task > cds watch_ )
- Start adding content, e.g. a [db/schema.cds](db/schema.cds), ...


## Learn more...

Learn more at https://cap.cloud.sap/docs/get-started/

## Deployment
This project can be deployed via a mta-file.
The deployment descriptor is mta.yaml.
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

## Azure DevOps Integration
Building and deploying the application can be done via Azure DevOps. SAP provides docker images to build and deploy the app. These images can be called via Azure DevOps.

* Create a new project in Azure DevOps
* Create a new pipeline
    * point to the github with the source code as the source
    * select a started pipeline


## Documentation
* [How to Use Project “Piper” Docker Images for CI/CD with Azure DevOps](https://blogs.sap.com/2019/10/24/how-to-use-project-piper-docker-images-for-cicd-with-azure-devops/)