# Steps to Integrate Terraform with Google Cloud Platform on Azure DevOps Pipeline (CI/CD)

1. On GCP (IAM), create a service account and assign the role of **Editor**.
2. Download the service account key (`.json` file).
3. On Azure DevOps, go to **Project Settings** -> **Pipelines** -> **Service Connections** -> **Create New Service Connection**.
4. Choose **Google Cloud Platform** as the service connection type for Terraform and click **Next**.
5. Open the service account key file (`.json` file).
6. Fill out the form as follows:
   
   6.1. Set **Project ID**.  
   6.2. Enter **Client Email** (the service account email).  
   6.3. Enter **Token URI**.  
   6.4. Enter **Private Key** (Note: include everything between `-----BEGIN PRIVATE KEY-----` and `-----END PRIVATE KEY-----`, i.e., the whole string between double quotes).  
   6.5. Set a **Service Connection Name** (choose a preferred name).  
   6.6. Check the box for **Grant access permission to all pipelines**.  
   6.7. Click **Done**.
8. From Azure DevOps, open **Marketplace** and install the **Terraform** extension.
9. Open your repository and upload or create your Terraform files (`.tf`).
10. Edit your pipeline file (`.yaml`).
11. Add the following two tasks to your pipeline:

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
