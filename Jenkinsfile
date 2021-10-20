pipeline {
  agent any
  stages {
    stage('Code Checkout') {
      steps {
        checkout([$class: 'GitSCM', branches: [[name: '**']], extensions: [[$class: 'CloneOption', noTags: false, reference: '', shallow: false]], userRemoteConfigs: [[credentialsId: 'github-app', url: 'https://github.com/okorach/demo-dotnet-jenkins']]])
      }
    }
    stage('SonarQube LATEST analysis - .Net') {
      steps {
        script {
          def dotnetScannerHome = tool 'Scanner for .Net Core'
          withSonarQubeEnv('SQ Latest') {
            sh "cd comp-dotnet; /usr/local/share/dotnet/dotnet ${dotnetScannerHome}/SonarScanner.MSBuild.dll begin /k:\"demo:github-jenkins-dotnet\" /n:\"GitHub / Jenkins / .Net Core\"" 
            sh "cd comp-dotnet; /usr/local/share/dotnet/dotnet build"
            updateGitlabCommitStatus name: 'jenkins', state: 'running'
            sh "cd comp-dotnet; /usr/local/share/dotnet/dotnet ${dotnetScannerHome}/SonarScanner.MSBuild.dll end"
            updateGitlabCommitStatus name: 'jenkins', state: 'running'
          }
        }
      }
    }
    stage("SonarQube LATEST Quality Gate - .Net") {
      steps {
        timeout(time: 5, unit: 'MINUTES') {
          script {
            def qg = waitForQualityGate()
            if (qg.status != 'OK') {
              updateGitlabCommitStatus name: 'jenkins', state: 'failed'
              echo ".Net component quality gate failed: ${qg.status}, proceeding anyway"
            }
          }
        }
      }
    }
  }
}