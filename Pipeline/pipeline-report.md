# Azure CI Pipeline YAML Report

Here's the YAML code for an example Azure CI Pipeline configuration:

```yaml
variables:
- name: BuildParameters.mavenPOMFile
  value: pom.xml
trigger:
  branches:
    include:
    - refs/heads/master
name: $(date:yyyyMMdd)$(rev:.r)
jobs:
- job: Job_1
  displayName: Agent job 1
  pool:
    name: Pool-1
  steps:
  - checkout: self
    fetchDepth: 1
  - task: Maven@3
    displayName: Maven Compile
    inputs:
      mavenPOMFile: $(BuildParameters.mavenPOMFile)
      goals: compile
  - task: Maven@3
    displayName: Maven Package
    inputs:
      mavenPOMFile: $(BuildParameters.mavenPOMFile)
  - task: CopyFiles@2
    displayName: 'Copy Files to: $(build.artifactstagingdirectory)'
    condition: succeededOrFailed()
    inputs:
      SourceFolder: $(system.defaultworkingdirectory)
      Contents: '**/*.jar'
      TargetFolder: $(build.artifactstagingdirectory)
  - task: PublishBuildArtifacts@1
    displayName: 'Publish Artifact: drop'
    condition: succeededOrFailed()
    inputs:
      PathtoPublish: $(build.artifactstagingdirectory)
  - task: SonarQubePrepare@5
    displayName: Analysis on SonarQube
    enabled: False
    inputs:
      SonarQube: 354e0a97-2f6c-40cd-a6b5-62aa97869de5
      scannerMode: CLI
      configMode: manual
      cliProjectKey: Secret-Santa
      cliProjectName: Secret-Santa
      extraProperties: sonar.java.binaries=.
  - task: SonarQubeAnalyze@5
    displayName: Run Code Analysis
    enabled: False
  - task: SonarQubePublish@5
    displayName: Publish Quality Gate Result
    enabled: False
  - task: dependency-check-build-task@6
    displayName: Dependency Check
    enabled: False
    inputs:
      projectName: Secret-Santa
      scanPath: .
  - task: CmdLine@2
    displayName: Command Line Script
    inputs:
      script: >
        java -jar target/*.jar
'''
This YAML code represents an Azure CI Pipeline configuration that includes various tasks for building, testing, and analyzing a Java application.
