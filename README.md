# Deploying SAP CAP application to SAP Cloud Platform using GitHub Actions

This example makes use of the SAP CAP BookShop Sample project. This sample uses NodeJS at the application layer and HANA as a database.
The original project can be found at [SAP Cloud CAP Samples](https://github.com/SAP-samples/cloud-cap-samples).

This exercise describes how this application can be deployed on the SAP Cloud Platform using GitHub Actions.
This exercise is based upon the SAP blog : [DevOps: CAP CI/CD with GitHub Actions](https://blogs.sap.com/2020/08/20/devops-cap-ci-cd-with-github-actions/)

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
 This command will look for the mta.yaml for the application definition. The generate mtar file will be available at `./mta_archives`directory.

 The mtar can be deployed on the SAP Cloud Platform using 
 ```
 cf deploy
 ```

 >Notes :
 >* to install the MBT build tool use
 >```
 >npm install -g mbt
 >```
 >* to build the mtar you need to have `make` installed for you OS. For windows you can use `chocolatey` for installation.
 >```
 >choco install make
 >```
 >* for deployment to SAP Cloud Platform, the `cf`-cli needs the `MTA plugin` to execute the `cf deploy` command. Install this using
 >```
 >cf add-plugin-repo CF-Community https://plugins.cloudfoundry.org
 >cf install-plugin multiapps
 >```

## Deployment using GitHub Actions
Building and deploying the application can also be done using GitHub actions.

* Create a new Workflow under github Actions
    * Insert the following code as pipeline : [deploySCP.yml](.github\workflows.yml)
* In the Build step, a linux vm is created, NodeJs and the MBT build tool are installed. The MTAR artifact is published in the last step.
* The Deploy steps picks up the artifact and Uses a predefined github action (https://github.com/Guerric-P) to deploy to the SAP Cloud Platform. The SAP Cloud Platform connections parameters are retrieved from`secrets`.
* Create Secrets under `Setting / Secrets` : these variables are used to login to SAP Cloud Platform.  
    * CF_USER = SAP Cloud Platform username
    * CF_PASSWORD = SAP Cloud Platform password
    * CF_API = SAP Cloud Platform login url (eg. https://api.cf.eu10.hana.ondemand.com)
    * CF_ORG = Organization 
    * CF_SPACE = Space (eg. dev)
* Execute a 'Push' on the master branch to stat the workflow.

>Note : the GitHub Action : https://github.com/Guerric-P. This `entrypoint.sh` contains the command to install the `MTA plugin` for `cf deploy`.

## Documentation
* [Cloud MTA Build Tool](https://sap.github.io/cloud-mta-build-tool/)
* [DevOps: CAP CI/CD with GitHub Actions](https://blogs.sap.com/2020/08/20/devops-cap-ci-cd-with-github-actions/)
