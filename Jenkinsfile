properties([
	parameters([
		string (name: 'branchName', defaultValue: 'master', description: 'Branch to get the test from')
	])
])

def isFailed = false
def branch = params.branchName
def artifactsToCopy = "src/PhpTravels.UITests/bin/Debug"
def buildArtifactsFolder = "C:\\Dev\\PowershellBuildArtifacts" //"C:\\BuildPackagesFromPipeline\\$BUILD_ID" 
currentBuild.description = "Branch: $branch"

def RunNUnitTests(String pathToDll, String condition, String reportName){
	try{
		bat "C:\\Dev\\ConsoleRunner\\nunit3-console.exe $pathToDll $condition --result=$reportName" 
	}finally{
		stash name: reportName, includes: reportName
	}
	
}

node('master') {
    stage('Checkout'){
        git branch: branch, url: 'https://github.com/mehhrunes/atata-phptravels-uitests.git'

    }
    
    stage('Restore NuGet'){
        //bat '"C:\\Dev\\nuget.exe" restore src/PhpTravels.UITests.sln'
        powershell ".\\build.ps1 RestoreNuGetPackages"
    }
	
	stage('Build Solution'){
	    //bat '"C:\\Program Files (x86)\\Microsoft Visual Studio\\2017\\Community\\MSBuild\\15.0\\Bin\\MSBuild.exe" src/PhpTravels.UITests.sln'
        powershell ".\\build.ps1 BuildSolution"
	}
	
	stage('Copy Artifacts'){
		//bat "(robocopy src/PhpTravels.UITests/bin/Debug $buildArtifactsFolder /MIR /XO) ^& IF %ERRORLEVEL% LEQ 1 exit 0"
        powershell ".\\build.ps1 CopyBuildArtifacts -SourceFolder $artifactsToCopy -DestinationFolder $buildArtifactsFolder"
    }
	
}

catchError
{
    isFailed = true
    stage('Run Tests')
    {
        parallel FirstTest: {
            node('master') {
                RunNUnitTests("$buildArtifactsFolder/PhpTravels.UITests.dll", "--where cat==FirstTest", "TestResult1.xml")
            }
        }, SecondTest: {
            node('Slave') {
                RunNUnitTests("$buildArtifactsFolder/PhpTravels.UITests.dll", "--where cat==SecondTest", "TestResult2.xml")
            }
        }
    }
    isFailed = false
}

node('master')
{
    stage('Reporting')
    {
        unstash "TestResult1.xml"
        unstash "TestResult2.xml"

        archiveArtifacts '*.xml'
        nunit testResultsPattern: 'TestResult1.xml, TestResult2.xml'

        if(isFailed)
        {
            slackSend color: 'danger', message: "Test run failed | ${env.JOB_NAME} ${env.BUILD_NUMBER} (<${env.BUILD_URL}|Open>)"
        }
        else
        {
            slackSend color: 'good', message: "Test run passed | ${env.JOB_NAME} ${env.BUILD_NUMBER} (<${env.BUILD_URL}|Open>)"
        }
    }
}