# Devops Pipelines

This directory contains templates to help build CluedIn solutions.

## Enable usage in your pipelines
You must configure a [Github service connection](https://docs.microsoft.com/en-us/azure/devops/pipelines/library/service-endpoints?view=azure-devops&tabs=yaml#sep-github) in Azure Devops to enable the templates in your pipelines.

Once configured, you can include this repository by adding the following to `azure-pipelines.yml`:
```yaml
# azure-pipelines.yml
resources:
  repositories:
    - repository: cluedin
      type: github
      name: CluedIn-Io/Azure
      endpoint: github
```
> NOTE: In this example the service connection is called `github`. You will need to change the `endpoint` value to match the name of your service connection

## [CluedIn.Crawler.yml](CluedIn.Crawler.yml)

This template specifies a job to manage the build, test, and pack of a crawler for CluedIn.

### Requirements
1. A [global.json](https://docs.microsoft.com/en-us/dotnet/core/tools/global-json?tabs=netcore3x) is specified in the root directory to manage the sdk version to use
1. Test projects have [coverlet](https://github.com/coverlet-coverage/coverlet#installation) added as a dependency
1. For code coverage, Azure Devops requires the [ReportGenerator](https://marketplace.visualstudio.com/items?itemName=Palmmedia.reportgenerator) extension to be installed into the organization

> NOTE: Full msbuild bin logs are created for each dotnet task.  You can find details on this format [here](https://github.com/dotnet/msbuild/blob/master/documentation/wiki/Binary-Log.md)

### Details
When added as part of your pipeline, the crawler job will perform the following tasks:
1. Set the version of the dotnet sdk to be used
1. Build the projects under the given root directory with the specified version
1. Run unit tests for the projects under the given root directory
1. Publish unit test results
1. Generate and publish code coverage reports
1. Run pack on the projects under the given root directory
1. Publish packed projects as a build asset
> Users can inject additional steps before build, after build, after test, and after pack

### Example usage
```yaml
jobs:
  - template: pipelines/CluedIn.Crawler.yml@cluedin
    parameters:
      configuration: Release
      version: 1.0.0
```