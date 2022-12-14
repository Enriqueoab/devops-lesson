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
        BaseRepoName = "DevopsLesson-"
    }

    stages {
        // Specify various stage with in stages

        // stage 1. Build
        stage ('Build'){
            steps {
                sh 'mvn clean install package'
            }
        }

        // Stage 2 : Testing
        stage ('Test'){
            steps {
                echo ' testing......'

            }
        }

        // Stage 3 : Publishing
        stage ('Publishing to Nexus'){
            steps {
                script{
                def NexusRepo = Version.endsWith("SNAPSHOT") ? "${BaseRepoName}SNAPSHOT"  : "${BaseRepoName}RELEASE"

                // def ArtifactFile = NexusRepo.endsWith("SNAPSHOT") ?
                //     "target/${ArtifactId}-${Version}-SNAPSHOT.war"  : "target/${ArtifactId}-${Version}.war"

                echo ' publishing...'
                nexusArtifactUploader artifacts:
                [[artifactId: "${ArtifactId}",
                classifier: '',
                file: "target/${ArtifactId}-${Version}.war",
                type: 'war']],
                credentialsId: '35c40cc9-2331-4b74-8067-e1ccc7852979',
                groupId: "${GroupId}",
                nexusUrl: "${NexusUrl}",
                nexusVersion: 'nexus3',
                protocol: 'http',
                repository: "${NexusRepo}",
                version: "${Version}"
            }
            }
        }

        
        // Stage 4 : Deploying the artifact to tomcat
        stage ('Deploy'){
            steps {
                echo 'deploying...'
                sshPublisher(publishers:
                 [sshPublisherDesc(
                    configName: 'AnsibleController',
                    transfers: [
                        sshTransfer(
                        cleanRemote: false, 
                        execCommand: 'ansible-playbook /opt/playbooks/downloadAndDeploy.yaml -i /opt/playbooks/hosts',
                        execTimeout: 120000,
                        )],
                    usePromotionTimestamp: false,
                    useWorkspaceInPromotion: false,
                    verbose: false)])
            }
        }
 
        // Stage 5 : Deploying the artifact to Docker
        stage ('Deploy to docker'){
            steps {
                echo 'deploying...'
                sshPublisher(publishers:
                 [sshPublisherDesc(
                    configName: 'AnsibleController',
                    transfers: [
                        sshTransfer(
                        cleanRemote: false, 
                        execCommand: 'ansible-playbook /opt/playbooks/downloadAndDeploy_Docker.yml -i /opt/playbooks/hosts',
                        execTimeout: 120000,
                        )],
                    usePromotionTimestamp: false,
                    useWorkspaceInPromotion: false,
                    verbose: false)])
            }
        }

        // Stage 6 : Publish the source code to Sonarqube
        stage ('Sonarqube Analysis'){
            steps {
                echo ' Source code published to Sonarqube for Static Code Analysis......'
                withSonarQubeEnv('sonarqube'){ // You can override the credential to be used
                     sh 'mvn sonar:sonar'
                }

            }
        }
    }
}