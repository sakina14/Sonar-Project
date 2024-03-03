pipeline{
    agent any
    environment {
        PATH = "$PATH:/opt/apache-maven-3.9.6/bin"
    }
    stages{
       stage('Checkout'){
            steps{
                git 'git@github.com:sakina14/Sonar-Project.git'
            }
       }
                 
       stage('Package'){
            steps{
                sh 'mvn clean package'
            }
           post {
               always {
                 junit '***/target/surefire-reports/TEST-*.xml'
         }
         }
         }
       
        stage('SonarQube analysis') {
//    def scannerHome = tool 'SonarScanner 4.0';
        steps{
        withSonarQubeEnv('SonarQube') { 
        // If you have configured more than one global server connection, you can specify its name
//      sh "${scannerHome}/bin/sonar-scanner"
        sh "mvn clean verify sonar:sonar -Dsonar.projectKey=sonar-project -Dsonar.host.url=http://65.0.182.238:9000/ -Dsonar.login=sqp_428ed6568f65838de3f5c5c7417813cc9e52f40f"
    }
        }
        }
       
    stage ('Deploy to Nexus') {
            steps {
      nexusArtifactUploader(
      nexusVersion: 'nexus3',
      protocol: 'http',
      nexusUrl: '15.206.170.231:8081',
      groupId: 'myGroupId',
      version: '1.0-SNAPSHOT',
      repository: 'maven-snapshots',
      credentialsId: 'nexuscred',
      artifacts: [
      [artifactId: 'maven-project',
      classifier: '',
      file: 'webapp/target/webapp.war',
      type: 'war']
      ])
      }
        }
        stage ('Deploy to Prod'){
     steps {
        sh 'scp -o StrictHostKeyChecking=no webapp/target/webapp.war root@13.233.133.5:/opt/apache-tomcat-8.0.52/webapps'
           }
   }
    post {
        success {
            emailext attachlog: true, body: 'Email sent out from Jenkins', subject: 'Test Email -Success', to: 'kaneezsakina98@gmail.com'
        }
        failure {
            emailext attachlog: true, body: 'Email sent out from Jenkins', subject: 'Test Email -Failed', to: 'kaneezsakina98@gmail.com'
        }
}    
}

