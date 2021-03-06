pipeline {
    agent any 
    tools { 
        maven 'Maven' 
        jdk 'java' 
    }
stages { 
     
 stage('Preparation') { 
     steps {
// for display purpose

      // Get some code from a GitHub repository

      checkout([$class: 'GitSCM', branches: [[name: '*/master']], doGenerateSubmoduleConfigurations: false, extensions: [], submoduleCfg: [], userRemoteConfigs: [[credentialsId: 'a70affd9-4166-4da4-9125-70d044e9c4df', url: 'https://github.com/jayakula/nojenkins.git']]])

      // Get the Maven tool.
     
 // ** NOTE: This 'M3' Maven tool must be configured
 
     // **       in the global configuration.   
     }
   }

   stage('Build') {
       steps {
       // Run the maven build

      //if (isUnix()) {
         sh 'mvn -Dmaven.test.failure.ignore=true install'
      //} 
      //else {
      //   bat(/"${mvnHome}\bin\mvn" -Dmaven.test.failure.ignore clean package/)
       }
//}
   }
 
  stage('Unit Test Results') {
      steps {
      junit '**/target/surefire-reports/TEST-*.xml'
      
      }
 }
 stage('Sonarqube') {
    environment {
        scannerHome = tool 'sonarqube'
    }
    steps {
        withSonarQubeEnv('sonarqube') {
            sh "${scannerHome}/bin/sonar-scanner"
        }
        timeout(time: 10, unit: 'MINUTES') {
            waitForQualityGate abortPipeline: true
        }
    }
}   
     stage('Artifact upload') {
      steps {
     nexusPublisher nexusInstanceId: 'nexus', nexusRepositoryId: 'maven-releases', packages: [[$class: 'MavenPackage', mavenAssetList: [[classifier: '', extension: '', filePath: '/var/lib/jenkins/workspace/pipeline-test/target/helloworld.war']], mavenCoordinate: [artifactId: 'helloworld', groupId: 'com.java.helloworld', packaging: 'war', version: '3.0']]]
      }
 }
    stage('Deploy War') {
      steps {
        deploy adapters: [tomcat8(credentialsId: 'tomcatuser', path: '', url: 'http://3.86.239.121:8080/')], contextPath: null, war: '**/*.war'
      }
 }
} 
    post {
        success {
            mail to:"jayachandraakula@gmail.com", subject:"SUCCESS: ${currentBuild.fullDisplayName}", body: "Build success"
        }
        failure {
            mail to:"jayachandraakula@gmail.com", subject:"FAILURE: ${currentBuild.fullDisplayName}", body: "Build failed"
        }
    }
}
