pipeline{
    agent any
    tools{
        maven 'M3'
    }
    environment {
        scannerHome = tool 'SonarScanner'
    }
    stages{
       stage('Cloning the Code'){
            steps{
                git changelog: false, poll: false, url: 'https://github.com/abnavemangesh22/MyNewPetClinic-2.git' 
            }
        }
        stage('static code analysis'){
          steps{
             script{
                 withSonarQubeEnv('sonar') {
                // This expands the evironment variables SONAR_CONFIG_NAME, SONAR_HOST_URL, SONAR_AUTH_TOKEN that can be used by any script.
                 sh 'echo $SONAR_HOST_URL'
                 sh 'echo $SONAR_AUTH_TOKEN'
                 sh 'mvn clean compile'
                 sh "${scannerHome}/bin/sonar-scanner -D sonar.projectKey=MangeshProject -Dsonar.java.binaries=target/classes"
               }
             } 
          }
        }
        stage('Packaging'){
            steps{
             sh 'mvn package -Dmaven.test.skip'
            }
        }
        stage('Artifact Uploader'){
            steps{
               nexusArtifactUploader artifacts: [[artifactId: 'spring-petclinic', classifier: '', file: 'target/spring-petclinic-1.5.2.jar', type: 'jar']], credentialsId: 'nexusserver', groupId: 'org.springframework.samples', nexusUrl: '192.168.226.133:32768', nexusVersion: 'nexus3', protocol: 'http', repository: 'maven-releases', version: '1.5.2' 
            }
        }
        stage('Docker build'){
            steps{
                sh 'docker build -t mangeshabnave/my-cgi-demo-v1.2-$BUILD_NUMBER .'
                //sh 'docker -H tcp://192.168.226.133:2375 build -t mangeshabnave/my-cgi-demo-v1.2-$BUILD_NUMBER .'
            }
        }
        stage('Docker Push'){
            steps{
                sh 'docker push mangeshabnave/my-cgi-demo-v1.2-$BUILD_NUMBER' //dockerhub public account
                sh 'docker -H tcp://192.168.226.133:2375 pull mangeshabnave/my-cgi-demo-v1.2-$BUILD_NUMBER'
                sh 'docker -H tcp://192.168.226.133:2375 run -dit -P mangeshabnave/my-cgi-demo-v1.2-$BUILD_NUMBER'
            }
        }
        
    }
    
 
}
