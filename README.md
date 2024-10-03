# Steps to Integate between Terraform and Google Cloud Platform on AzureDevops Pipeline (CI/CD).

1- on GCP (IAM) create service account and assige role (editor)
2- Download the service account key (.json)
3- on AzureDevops (project settings)->(pipelines)->(service connections) -> (create new service connections).
4-  Choose a service or connection type (GCP for Terraform) and click Next 
5- open service account key file (.json file)
6- fill the form as the following :
6.1- set Project id 
6.2- Client email (service accoutn mail )
6.3- Token uri
6.4- Private Key # Note include it from -----BEGIN PRIVATE KEY----- till -----END PRIVATE KEY-----\n  i mean the whole string between double qoutes.
6.5- Service connection name  set preffer name you want.
6.6- mark check box for secuirty (Grant access permission to all pipelines)
6.7- click done.
7- from Azuredevops open marketplace and install Terraform.
8- open your repo and upload or create your terraform files (.tf)
9- edit your pipeline file (.yaml)
10- add the below two taks 


    - task: TerraformTaskV4@4
      inputs:
        provider: 'gcp'
        command: 'init'
        backendServiceGCP: 'set your service connectio name'
        backendGCPBucketName: 'set bucket name to save terraform state on it '
        backendGCPPrefix: 'set your prefix such as terraform/state'
        #if you add your terraform files inside directory you can refere to it using workingDirectory
        workingDirectory: '$(Build.SourcesDirectory)/terraform_directory'

    - task: TerraformTaskV4@4
      inputs:
        provider: 'gcp'
        command: 'apply'
        commandOptions: '-lock=false'
        environmentServiceNameGCP: 'set bucket name to save terraform state on it'
        #if you add your terraform files inside directory you can refere to it using workingDirectory
        workingDirectory: '$(Build.SourcesDirectory)/terraform_directory'
