
pipeline {
  agent any
  stages {
   stage('SonarQube LATEST analysis - .Net') {
     steps {
       withSonarQubeEnv('SQ Latest', envOnly: true) {
         script {
           sh 'cd comp-dotnet; /usr/local/share/dotnet/dotnet sonarscanner begin /k:"demo:github-comp-dotnet" /n:"GitHub project - .Net Core" /d:"sonar.host.url=${env.SONAR_HOST_URL}" /d:"sonar.login=${env.SONAR_AUTH_TOKEN}"; /usr/local/share/dotnet/dotnet build; /usr/local/share/dotnet/dotnet sonarscanner end /d:"sonar.login=${env.SONAR_AUTH_TOKEN}"'
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
             echo ".Net component quality gate failed: ${qg.status}, proceeding anyway"
           }
           sh 'rm -f comp-dotnet/build/sonar/report-task.txt'
         }
       }
     }
   }
  }
}