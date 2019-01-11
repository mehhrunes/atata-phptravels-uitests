node('master') {
    stage('Checkout'){
        git 'https://github.com/mehhrunes/atata-phptravels-uitests.git'
    }
    
    stage('Restore NuGet'){
        bat '"C:\\Dev\\nuget.exe" restore src/PhpTravels.UITests.sln'
    }
	
	stage('Build Solution'){
		bat '"C:\\Program Files (x86)\\Microsoft Visual Studio\\2017\\Community\\MSBuild\\15.0\\Bin\\MSBuild.exe" src/PhpTravels.UITests.sln'
	}
	
	stage('Run Tests'){
		bat '"C:\\Dev\\ConsoleRunner\\nunit3-console.exe" src\\PhpTravels.UITests\\bin\\Debug\\PhpTravels.UITests.dll'
	}
}