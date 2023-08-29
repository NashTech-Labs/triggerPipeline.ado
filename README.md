# triggerPipeline


## Pipeline Requirements

The trigger pipeline requires the following parameters to be defined:
Paramaters:


| Name  | type | Default | Values | Opional/Required | Comments |
| ------------- | ------------- | ------------- | ------------- | ------------- | ------------- |
| pipelineId | string | | | Required | The definition id of the pipeline to be triggered |
| AzureDevOpsRestApiBuildUserAccessToken | string | | | Required | Already setup in 'Build Variables' pipeline library. The PAT to be used while calling the rest api endpoint |
| ado.runPipelineURL | string | | | Required | Already setup in 'AFS Build Variables' pipeline library. The rest endpoint to trigger a pipeline run |
| pipelineVariable | string | | | Optional | Queue time pipeline variables to passed while triggering the pipeline. This should be a json format |
| pipelineDisplayName | string | | | Optional | Display name of the pipeline to be triggered. This would show up in step display name |
| setBuildIdInOutput | Boolean | true | true/false | Optional | Indicates whether to set the triggered pipeline build id in ouput variable or not |
| buildIdVariableName | string | | | Optional | Must be set when setBuildIdInOutput is 'true'. The template will use this variable name to set the build id in output varaible for further usage in the job/step |
| stepName | string | 'TriggerPipeline' | | Optional | Name of the step |
| executeCondition | string | | | Optional | Condition on which this step to be run |


  These parameters provide multiple use case options for the triggerPipeline template pipeline.

### Variables:

In order to use these templates in pipeline, following pipeline variables must be set:

| Name  | Opional/Required | Value | Comments |
| :------------- | :-------------: | :-------------: | :------------- |
| build.triggerDeployment | Required | true | Already set in 'AFS Build Variables' pipeline library. The rest api url to get the latest build id |
| build.defaultBranch | Required | 'refs/heads/master' | Branch to be considered |

 



  ## Use Case


### Using triggerPipeline template in a pipeline

The following example showcases how to use this template in a systematic approach. For example: 

```yaml

 # azure-pipeline.yaml
  resources:
    repositories:
      - repository: Template
        type: github
         name:  your_username/Repo_name
        ref: <respective branch name>
        endpoint: 'githubServiceConnectioNname'

  stages:
  - stage: Template_testing 
    jobs:
      - job: Job2
        displayName: TestingJob2
        steps:
        - checkout: none
        - template: frameWork/common/pipeline/triggerPipeline.yml@Template
          parameters:
            stepName: emsOrchestratorRunCI
            pipelineDisplayName: 'ems orchestrator'
            pipelineId: 1767
            pipelineVariables: |
              [
                {
                  "name": "system.debug",
                  "value": "false"             
                }
              ]
            pipelineParameters: |
              [
                {
                  "name": "dockerfilePath",
                  "value": "Dockerfile"          
                }
              ]  
            setBuildIdInOutput: true
            buildIdVariableName: Anand
            executeCondition: and(
              succeeded(), 
              ne(variables['build.triggerDeployment'], 'false'),
              eq(variables['Build.SourceBranch'], variables['build.defaultBranch']))
   



```

The parameters are provided to configure the triggerPipeline template into the pipeline according to the desired build configuration and stages.

Make sure to adjust the repository name, branch name, and parameter values according to your project's requirements.
