pipeline{
    //Directives (agent, toolsenvironments,..)
    agent any
    tools {
        maven 'maven'
    }
    //Create variables to store values of our pom.xml
    environment{
        ArtifactId = readMavenPom().getArtifactId()
        Version = readMavenPom().getVersion()
        Name = readMavenPom().getName()
        GroupId = readMavenPom().getGroupId()
        NexusUrl = "172.20.10.157:8081"
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
                nexusArtifactUploader artifacts:
                [[artifactId: "${ArtifactId}",
                classifier: '',
                file: 'target/devopslesson-0.1.9-SNAPSHOT.war',
                type: 'war']],
                credentialsId: '35c40cc9-2331-4b74-8067-e1ccc7852979',
                groupId: "${GroupId}",
                nexusUrl: "${NexusUrl}",
                nexusVersion: 'nexus3',
                protocol: 'http',
                repository: 'DevopsLesson-SNAPSHOT',
                version: "${Version}"
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