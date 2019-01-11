node('master') {
    stage('Checkout'){
        git 'https://github.com/mehhrunes/atata-phptravels-uitests.git'
    }
    
    stage('Restore NuGet'){
        bat '"C:\\Dev\\nuget.exe" restore src/PhpTravels.UITests.sln'
    }
}