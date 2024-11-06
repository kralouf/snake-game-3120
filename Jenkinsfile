node('ubuntu-Appserver-3120')
{
    def app
    stage('Cloning Git')
    {
    /* Let's make sure we have the repository cloned to our workspace */
    checkout scm
    }
 
      stage('SCA-SAST-SNYK-TEST') 
      {
        agent any
        script {
         snykSecurity(
            snykInstallation: 'Snyk',
            snykTokenId: 'Snykid',
            severity: 'critical'
         )
      }
    }
    stage('Sonarqube-Analyze')
    {
        agent {
            label 'ubuntu-Appserver-3120'
        }
        steps {
            script {
                def scannerHome = tool 'SonarQubeScanner'
                withSonarQubeEnv('sonarqube') {
                    sh "${scannerHome}/bin/sonar-scanner \
                        -Dsonar.projectKey=gameapp
                        -Dsonar.sources=."
                }
            }
        }
    }
    stage('Build-and-Tag')
    {
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
    stage('Post-to-dockerhub')
    {
        agent {
            label 'ubuntu-Appserver-3120'
        }
        steps {
            script {
                docker.withRegistry('https://registry.hub.docker.com', 'dockerhub_credentials')
                {
                    app.push("latest")
                }
            }
        }
    }
    stage('Pull-image-server')
    {
        sh "docker-compose down"
        sh "docker-compose up -d"
    }
 
}