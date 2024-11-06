pipeline {
    agent none
    stages {
        stage('CLONE GIT REPOSITORY') {
            agent {
                label 'ubuntu-Appserver-3120'
            }
            steps {
                checkout scm
            }
        }  
 
        stage('SCA-SAST-SNYK-TEST') {
            agent any
            steps {
                script {
                    snykSecurity(
                        snykInstallation: 'Snyk',
                        snykTokenId: 'Snykid',
                        severity: 'critical'
                    )
                }
            }
        }
 
        stage('SonarQube Analysis') {
            agent {
                label 'ubuntu-Appserver-3120'
            }
            steps {
                script {
                    def scannerHome = tool 'SonarQubeScanner'
                    withSonarQubeEnv('sonarqube') {
                        sh "${scannerHome}/bin/sonar-scanner \
                            -Dsonar.projectKey=gameapp \
                            -Dsonar.sources=."
                    }
                }
            }
        }
 
        stage('BUILD-AND-TAG') {
            agent {
                label 'ubuntu-Appserver-3120'
            }
            steps {
                script {
                    def app = docker.build("lkraimer/snake_game_3120")
                    app.tag("latest")
                }
            }
        }
 
        stage('POST-TO-DOCKERHUB') {    
            agent {
                label 'ubuntu-Appserver-3120'
            }
            steps {
                script {
                    docker.withRegistry('https://registry.hub.docker.com', 'dockerhub_credentials') {
                        def app = docker.image("lkraimer/snake_game_3120")
                        app.push("latest")
                    }
                }
            }
        }
 
        stage('DEPLOYMENT') {    
            agent {
                label 'ubuntu-Appserver-3120'
            }
            steps {
                sh "docker-compose down"
                sh "docker-compose up -d"   
            }
        }
    }
}