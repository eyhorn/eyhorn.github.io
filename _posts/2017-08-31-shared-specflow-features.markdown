---
layout: post
title:  "Sharing SpecFlow features between test projects"
date:   2017-08-31 11:24:00 +1100
categories: specflow tests
---
Depending on how the team works it might be useful to share same [SpecFlow](http://specflow.org/)  feature files between different test projects. For example same feature file can be used for Unit and Acceptance tests.

I have setup [small project](https://github.com/eyhorn/shared-specflow-feature) to demonstrate one of the ways the feature files can be shared between different projects in the same solution. 
The approach used in this example is to use one project as a source and copy new and changed feature files to another project on build.
First lets take a look at the feature file 
{% highlight gherkin %}
Feature: HelloWorld
	In to see that a program works
	As programmer
	I want to see hello world

Scenario: Print Hello World
	Given the SharedFeature program
	When SharedFeature program is started
	Then the 'Hello World' should be printed on the screen
{% endhighlight %}

This scenario can be tested as a unit test
{% highlight c# %}
    [Binding]
    public class HelloWorldSteps
    {
        private HelloWorldService _helloWorldService;
        private Mock<IOutputWriter> _outputWriterMoq;

        [Given(@"the SharedFeature program")]
        public void GivenTheSharedFeatureProgram()
        {
            _outputWriterMoq = new Mock<IOutputWriter>();
            _helloWorldService = new HelloWorldService(_outputWriterMoq.Object);
        }

        [When(@"SharedFeature program is started")]
        public void WhenSharedFeatureProgramIsStarted()
        {
            _helloWorldService.SayHello();
        }

        [Then(@"the '(.*)' should be printed on the screen")]
        public void ThenTheShouldBePrintedOnTheScreen(string expectedString)
        {
            _outputWriterMoq.Verify(writer => writer.WriteLine(expectedString));
        }
    }
{% endhighlight %}

The same scenario can be tested as a `black box` and used for acceptance testing
{% highlight c# %}
 [Binding]
    public class HelloWorldSteps
    {
        private readonly Process _process;
        private readonly TaskCompletionSource<string> _processOutputTaskCompletionSource;

        public HelloWorldSteps(Process process)
        {
            _process = process;
            _processOutputTaskCompletionSource = new TaskCompletionSource<string>();
        }

        [Given(@"the SharedFeature program")]
        public void GivenTheSharedFeatureProgram()
        {
            _process.StartInfo = new ProcessStartInfo
            {
                FileName = "SharedFeature.exe",
                UseShellExecute = false,
                RedirectStandardOutput = true,
                CreateNoWindow = true,
                WindowStyle = ProcessWindowStyle.Hidden
            };
            _process.OutputDataReceived += OutputHandler;
        }

        private void OutputHandler(object sender, DataReceivedEventArgs e)
        {
            if (_processOutputTaskCompletionSource.Task.IsCompleted) return;
            _processOutputTaskCompletionSource.SetResult(e.Data);
        }

        [When(@"SharedFeature program is started")]
        public void WhenSharedFeatureProgramIsStarted()
        {
            _process.Start();
            _process.BeginOutputReadLine();
        }

        [Then(@"the '(.*)' should be printed on the screen")]
        public void ThenTheShouldBePrintedOnTheScreen(string expectedString)
        {
            var processOutput = _processOutputTaskCompletionSource.Task.Result;
            Assert.Equal(expectedString, processOutput);
        }
    }
{% endhighlight %}

To accomplish feature sharing target project has to have following in `csproj` file.

* Import of SpecFlow targets
{% highlight xml %}
<Import Project="..\packages\SpecFlow.2.2.0\tools\TechTalk.SpecFlow.targets" Condition="Exists('..\packages\SpecFlow.2.2.0\tools\TechTalk.SpecFlow.targets')" />
{% endhighlight %}
* Targets that copy `feature` files and regenerate code-behind
{% highlight xml %}
  <Target Name="BeforeBuild">
    <CallTarget Condition="'$(NCrunch)' != '1'" Targets="CopyFeatureFilesIfNewer" />
    <CallTarget Condition="'$(NCrunch)' != '1'" Targets="RegenFeatureCodeBehind" />
  </Target>
  <Target Name="CopyFeatureFilesIfNewer">
    <Exec Command="xcopy $(SolutionDir)Tests\Features\*.feature $(SolutionDir)AcceptanceTests\Features\ /d /y /i" />
  </Target>
  <Target Name="RegenFeatureCodeBehind">
    <GenerateAll ShowTrace="$(ShowTrace)" BuildServerMode="$(BuildServerMode)" OverwriteReadOnlyFiles="$(OverwriteReadOnlyFiles)" ProjectPath="$(MSBuildProjectFullPath)" ForceGeneration="$(ForceGeneration)" VerboseOutput="$(VerboseOutput)">
      <Output TaskParameter="GeneratedFiles" ItemName="SpecFlowGeneratedFiles" />
    </GenerateAll>
  </Target>
{% endhighlight %}
<sub>Note: This solution is only applicable if the projects are in the same repository 
and feature files will not be copied if tests are run by NCrunch</sub>

 
