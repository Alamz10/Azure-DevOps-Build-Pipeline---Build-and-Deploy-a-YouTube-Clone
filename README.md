# Azure-DevOps-CI/CD-Deploy-YouTube-Clone
## Steps to set the infrastructure
- Login to VSCode or any other IDE of your choice
- Run the below commands to download the application code

```
  git init
  git clone https://github.com/piyushsachdeva/Youtube_Clone
```
- Create a project in Azure DevOps and push the code by running the below commands on VSCode:

```
git remote add origin
git push -u origin all
Note: Make sure to update your Azure repo in the above command
```
Go to the Azure Portal and Create the Azure App Service
Implement the build pipeline using the classic editor

## Pipeline code used in the demo

``` YAML

trigger: 
- main

stages:
- stage: Build
  jobs:
  - job: Build
    pool:
      vmImage: 'ubuntu-latest'
    steps:
    - task: Npm@1
      inputs:
        command: 'install'
    - task: Npm@1
      inputs:
        command: 'custom'
        customCommand: 'run build'

    
    - task: PublishBuildArtifacts@1
      inputs:
        PathtoPublish: 'build'
        ArtifactName: 'drop'
        publishLocation: 'Container'

- stage: Deploy 
  jobs:
  - job: Deploy
    pool:
      vmImage: 'ubuntu-latest'
    steps:
    - task: DownloadBuildArtifacts@1
      inputs:
        buildType: 'current'
        downloadType: 'single'
        artifactName: 'drop'
        downloadPath: '$(System.ArtifactsDirectory)'
    - task: AzureRmWebAppDeployment@4
      inputs:
        ConnectionType: 'AzureRM'
        azureSubscription: 'Azure subscription 1(79cacc4c-2a64-46f3-a90f-56e5c46961cc)'
        appType: 'webAppLinux'
        WebAppName: 'Youtubeapp'
        packageForLinux: '$(System.ArtifactsDirectory)/drop'
        RuntimeStack: 'STATICSITE|1.0'
```
