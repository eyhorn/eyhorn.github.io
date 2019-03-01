---
layout: post
title:  "Sharing Octopus Deploy variables between projects"
date:   2019-03-01 14:20:00 +1100
categories: octopus devops cd
---
There is no straightforward way to share transformed variables between projects in the [Octopus Deploy](https://octopus.com/).
The solution is to use Octopus API and PowerShell:

1. In the library create script module `ShareVariables` using content of the snippet below as the body for the module

    ```powershell
    function Get-SharedVariableNames {
        return @("VariableName1", "VariableName2", "VariableName3")
    }

    function Set-SharedVariables {
        $variablesToSet = Get-SharedVariableNames
        foreach ($variableName in $variablesToSet) {
            Set-OctopusVariable -name "Shared.$($variableName)" -value $OctopusParameters[$variableName]
        }
    }

    function Read-SharedVariables {
        $octopusURL = $OctopusParameters["Octopus.Web.BaseUrl"]
        $projectName = "name of the project you a reading variables from"
        $environment = $OctopusParameters["Octopus.Environment.Id"]
        $tenant = $OctopusParameters["Octopus.Deployment.Tenant.Id"]
        $octopusApiKey = "use you Octopus api key"
        $header = @{ "X-Octopus-ApiKey" = octopusApiKey }

        $project = (Invoke-RestMethod $octopusURL/api/projects?name=$projectName -Headers $header -UseBasicParsing).Items | Where-Object Name -eq $projectName
        $getDeploymentUrl = "$($octopusURL)/api/deployments?projects=$($project.Id)&environments=$($environment)&tenants=$($tenant)&taskState=success&take=1"
        $deployment = (Invoke-RestMethod $getDeploymentUrl -Headers $header -UseBasicParsing).Items | Select-Object -first 1
        $variables = (Invoke-RestMethod $octopusURL/api/variables/variableset-$($deployment.Id) -Headers $header -UseBasicParsing).Variables

        $variablesToSet = Get-SharedVariableNames
        foreach ($variableName in $variablesToSet) {
            $variable = $variables | Where-Object Name -Like "*.Output.Shared.$($variableName)" | Select-Object -first 1
            Set-OctopusVariable -name $variableName -value "$($variable.Value)"
        }
    }
    ```

2. Configure the script module
   1. In the function `Get-SharedVariableNames` provide names of the variables that require sharing
   2. In the function `Read-SharedVariables`:
      1. Set `$projectName` to the name of project you are reading variables from
      2. Set `$octopusApiKey` to the [Api key](https://octopus.com/docs/api-and-integration/api/how-to-create-an-api-key) that you have created
   3. If you do not use tenants in your deployments you might need to remove `tenant` url parameter from the `$getDeploymentUrl`
3. In the source project:
   1. Include script module `ShareVariables`
   2. Add a script step `Set Shared Variables`
   3. As a script body use `Set-SharedVariables`
4. In the target deployment project
   1. Include script module `ShareVariables`
   2. For each step that requires shared variables:
      1. Enable `Custom Deployment scripts` in [Configuration features](https://octopus.com/docs/deployment-process/configuration-features)
      2. In the `Pre-deployment script` set the body of the script to `Read-SharedVariables`

The limitation of this solution is that you will always use latest set of variables from the source project but for the majority of the deployments this shouldn't be a problem.