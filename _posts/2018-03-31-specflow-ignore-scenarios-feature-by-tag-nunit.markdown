---
layout: post
title:  "Ignore SpecFlow scenarios and feature by tag for NUnit"
date:   2018-03-31 11:24:00 +1100
categories: specflow tests nunit
---
In my earlier [post](/specflow/tests/xunit/2018/02/22/specflow-ignore-scenarios-by-tag-xunit.html) I have described how to ignore SpecFlow scenarios with xUnit runner. Now I want to show how to achieve the same goal for NUnit runner. With xUnit it was possible to skip individual scenarios. With NUnit whole feature can be skipped

I have prepared example for the `NUnit runner` that can be found [here](https://github.com/eyhorn/specflow-ignore-by-tag-nunit).
In the listing below one of the scenarios has been tagged `@Superseded` and will be ignored during execution.
{% highlight gherkin %}
Feature: Calculations
	In order to perform basic calculations
    As a user of this program
    I want to be told results of calculations


Scenario Outline: Perform basic operations
	Given I have provided <First Number> and a <Second Number>
	When I perform operation '<Operation>'
	Then the result should be <Result Number>

Examples: 
    | Operation | First Number | Second Number | Result Number |
    | Add       | 5            | 7             | 12            |
    | Add       | 3            | 4             | 7             |
    | Sub       | 4            | 1             | 3             |
    | Sub       | 987          | 342           | 645           |
    | Multiply  | 7            | 3             | 21            |
    | Multiply  | 14           | 12            | 168           |
    | Divide    | 121          | 11            | 11            |
    | Divide    | 63           | 7             | 9             |
    
@Superseded
Scenario: Perform calculations
    Given An outdated test
    When I perform outdated operation
    Then Some Result will be produced    
{% endhighlight %}
Another example would be a draft of the feature
{% highlight gherkin %}
@Draft
Feature: NewFunctions

Scenario: Draft scenario
	Given I have a number 90	
	When I take sinus of the number
	Then I will receive 1
{% endhighlight %}

To get this working you need to add `FeatureGeneration.targets` file to your project
{% highlight xml %}
<?xml version="1.0" encoding="utf-8"?>
<Project ToolsVersion="4.0" DefaultTarget="GeneratePatchedFeatureFiles" xmlns="http://schemas.microsoft.com/developer/msbuild/2003">
  <Import Project="..\packages\SpecFlow.2.3.0\tools\TechTalk.SpecFlow.targets" Condition="Exists('..\packages\SpecFlow.2.3.0\tools\TechTalk.SpecFlow.targets')" />
     
  <Target Name="GeneratePatchedFeatureFiles" DependsOnTargets="UpdateFeatureFilesInProject">
    <UpdateFeatureFile FeatureFiles="@(SpecFlowFeatureFiles->'%(RelativeDir)\%(Filename).feature.cs')" SkippedTags ="Superseded|Manual|Draft"/>
  </Target>
  
  <UsingTask TaskName="UpdateFeatureFile" TaskFactory="CodeTaskFactory" AssemblyFile="$(MSBuildToolsPath)\Microsoft.Build.Tasks.v4.0.dll">
    <ParameterGroup>
      <FeatureFiles ParameterType="Microsoft.Build.Framework.ITaskItem[]" Required="true" />
      <SkippedTags ParameterType="System.String" Required="true" />
    </ParameterGroup>
    <Task>
      <!--
      Go through generated feature.cs files and patch if not already patched
      Patching will skip all scenarios marked with predefined tags
      -->
      <Reference Include="System.Core" />
      <Using Namespace="System" />
      <Using Namespace="System.IO" />
      <Using Namespace="System.Text.RegularExpressions" />
      <Code Type="Fragment" Language="cs">
        <![CDATA[      
        foreach(var featureFile in FeatureFiles) {            
            var automaticallyPatchedFlag = "//Automatically patched";
            var input = File.ReadAllText(featureFile.ToString());
            if(input.EndsWith(automaticallyPatchedFlag)) continue;
            
            Log.LogMessage("Patching feature file " + featureFile.ToString());
            input = Regex.Replace(input, @"\[NUnit.Framework.CategoryAttribute\(""("+ SkippedTags + @")""\)\]",
              "$&\r\n[NUnit.Framework.IgnoreAttribute(\"Test not run as it was tagged: $1\")]", RegexOptions.IgnoreCase);
            input += automaticallyPatchedFlag;
            File.WriteAllText(featureFile.ToString(), input);
        }
          ]]>
      </Code>
    </Task>
  </UsingTask>
</Project>
{% endhighlight %}
Then add following lines to your .csproj file 
{% highlight xml %}
  <Import Project="FeatureGeneration.targets" />
  <Target Name="BeforeBuild" DependsOnTargets="GeneratePatchedFeatureFiles" />
{% endhighlight %}

Doing this will automatically patch `*.feature.cs` on build and scenarios marked with tags specified in `FeatureGeneration.targets` will not be executed.
