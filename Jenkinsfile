pipeline{
    //Directives (agent, toolsenvironments,..)
    agent any
    tools {
        maven 'maven'
    }

    stages {
        // Specify various stage with in stages

        // stage 1. Build
        stage ('Build'){
            steps {
                sh 'mvn clean install package'
            }
        }

        // Stage2 : Testing
        stage ('Test'){
            steps {
                echo ' testing......'

            }
        }

        // Stage3 : Deploying
        stage ('Publishing to Nexus'){
            steps {
                echo ' publishing...'
                nexusArtifactUploader artifacts: [[artifactId: 'devopslesson',
                classifier: '', file: 'target/devopslesson-0.1.9-SNAPSHOT.war',
                type: 'war']], credentialsId: '8987bc36-3605-4f47-b09f-4b5a8175906c',
                groupId: 'com.DevopsLesson', nexusUrl: '172.20.10.157:8081',
                nexusVersion: 'nexus3', protocol: 'http', repository: 'DevopsLesson-SNAPSHOT',
                version: '0.1.9-SNAPSHOT'
            }
        }

        
        // Stage4 : Deploying
        stage ('Deploy'){
            steps {
                echo 'deploying...'
            }
        }

        // // Stage3 : Publish the source code to Sonarqube
        // stage ('Sonarqube Analysis'){
        //     steps {
        //         echo ' Source code published to Sonarqube for SCA......'
        //         withSonarQubeEnv('sonarqube'){ // You can override the credential to be used
        //              sh 'mvn sonar:sonar'
        //         }

        //     }
        // }
    }
}