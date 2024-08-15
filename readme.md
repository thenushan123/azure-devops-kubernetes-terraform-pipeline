# YAML Pipeline Configuration

This document provides an annotated version of the YAML pipeline configuration. Each section is explained with inline comments to help understand the pipeline structure and functionality.

```yaml
# Define the branches that trigger the pipeline
trigger:
- main # The pipeline will run when there are changes in the 'main' branch

# Define the virtual machine image for the build agent
pool:
  vmImage: ubuntu-latest # Use the latest version of Ubuntu for the build agent

# Define the stages of the pipeline
stages:
- stage: Build
  # The 'Build' stage
  jobs:
  - job: FirstJob
    # Job within the 'Build' stage
    steps:
    - bash: echo Build/FirstJob
      # Execute a bash command to print "Build/FirstJob"
    - bash: echo $(PipelineLevelVariable)
      # Print the value of 'PipelineLevelVariable', which is not defined in this YAML

  - job: SecondJob
    # Another job within the 'Build' stage
    steps:
    - bash: echo Build/SecondJob
      # Execute a bash command to print "Build/SecondJob"

- stage: DevDeploy
  dependsOn: Build
  # The 'DevDeploy' stage depends on the successful completion of the 'Build' stage
  variables:
    environment: DevDeploy
    # Define a variable 'environment' with the value 'DevDeploy'
  jobs:
  - job: FirstJob
    # Job within the 'DevDeploy' stage
    steps:
    - bash: echo $(environment)/FirstJob
      # Print the value of the 'environment' variable, which is 'DevDeploy', followed by "/FirstJob"

- stage: QADeploy
  dependsOn: Build
  # The 'QADeploy' stage depends on the successful completion of the 'Build' stage
  variables:
    environment: QADeploy
    # Define a variable 'environment' with the value 'QADeploy'
  jobs:
  - job: FirstJob
    # Job within the 'QADeploy' stage
    steps:
    - bash: echo $(environment)/FirstJob
      # Print the value of the 'environment' variable, which is 'QADeploy', followed by "/FirstJob"

- stage: ProdDeploy
  dependsOn:
  - DevDeploy
  - QADeploy
  # The 'ProdDeploy' stage depends on the successful completion of both 'DevDeploy' and 'QADeploy' stages
  variables:
    environment: ProdDeploy
    # Define a variable 'environment' with the value 'ProdDeploy'
  jobs:
  - job: FirstJob
    # Job within the 'ProdDeploy' stage
    steps:
    - bash: echo $(environment)/FirstJob
      # Print the value of the 'environment' variable, which is 'ProdDeploy', followed by "/FirstJob"
