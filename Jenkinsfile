/*
pipeline {

    agent {
        node {
            label 'master'
        }
    }
    tools {
        maven 'Maven'
    }

    options {
        buildDiscarder logRotator( 
                    daysToKeepStr: '16', 
                    numToKeepStr: '10'
            )
    }

    stages {
        
        stage('Cleanup Workspace') {
            steps {
                cleanWs()
                sh """
                echo "Cleaned Up Workspace For Project"
                """
            }
        }

        stage('Code Checkout') {
            steps {
                checkout([
                    $class: 'GitSCM', 
                    branches: [[name: 'main']], 
                    userRemoteConfigs: [[url: 'https://github.com/J-Phiz/spring-petclinic.git/']]
                ])
            }
        }

        stage('Unit Testing') {
            steps {
                sh """
                echo "Running Unit Tests"
                mvn -B test
                """
            }
        }

        stage('Build Code') {
            steps {
                sh """
                echo "Building Artifact"
                mvn -Dmaven.test.skip=true -DskipTests -B package
                """
            }
        }

        stage('Deploy Code') {
            when {
                branch 'main'
            }
            steps {
                sh """
                echo "Deploy to Artifactory"
                """
            }
        }
    }   
}
*/

pipeline {

    agent {
        node {
            label 'master'
        }
    }
    
    tools {
        maven 'Maven'
    }
    
    options {
        buildDiscarder logRotator( 
                    daysToKeepStr: '16', 
                    numToKeepStr: '10'
            )
    }

    stages {
        stage('Cleanup Workspace') { 
            steps {
                cleanWs()
                sh """
                echo "Cleaned Up Workspace For Project"
                """
            }
        }

        stage('Code Checkout') {
            steps {
                checkout([
                    $class: 'GitSCM', 
                    branches: [[name: '*/main']], 
                    userRemoteConfigs: [[url: 'https://github.com/J-Phiz/spring-petclinic.git/']]
                ])
            }
        }

        stage('Tests and Build') {
            steps {
                parallel(
                    tests: {    
                        sh """
                        echo "Running Unit Tests"
                        mvn -B test
                        """
                    },
                    build: {
                        sh """
                        echo "Building Artifact"
                        mvn -Dmaven.test.skip=true -DskipTests -B package
                        """
                    }
                )
            }
        }
        
        stage('Upload to Artifactory') {
            steps {
                rtUpload (
                    // Obtain an Artifactory server instance, defined in Jenkins --> Manage Jenkins --> Configure System:
                    serverId: 'jphiz',
                    spec: """{
                            "files": [
                                    {
                                        "pattern": "target/spring-petclinic*.jar",
                                        "target": "default-maven-local/petclinic/2.5.0/spring-petclinic-2.5.0.jar"
                                    }
                                ]
                            }"""
                )
            }
        }
        
        stage('Publish build info to Artifactory') {
            steps {
                rtPublishBuildInfo (
                    serverId: 'jphiz'
                )
            }
        }
    }
}
