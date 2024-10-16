pipeline
{
    agent none
    stages
    {
        stage('Cloning Git')
        {
            agent
            {
                label 'ubuntu-Appserver-3120'
            }
            steps
            {
                checkout scm
            }
        }
        stage('SCA-SAST-Snyk-Test')
        {
            agent
            {
                label 'ubuntu-Appserver-3120'
            }
            steps
            {
                echo "Testing Snyk, Testing Snyk 1, 2, 3....."
            }
        }
        stage('Build-and-Tag')
        {
            agent
            {
                label 'ubuntu-Appserver-3120'
            }
            steps
            {
                script
                {
                    def app = docker.build("lkraimer/snake_game_3120")
                    app.tag("latest")
                }
            }
        }
        stage('Post-to-DockerHub')
        {
            agent
            {
                label 'ubuntu-Appserver-3120'
            }
            steps
            {
                script
                {
                    docker.withRegistry("https://registry.hub.docker.com", "dockerhub_credentials")
                    {
                        def app = docker.image("lkraimer/snake_game_3120")
                        app.push("latest")
                    }
                }
            }
        }
        stage('Deployment')
        {
            agent
            {
                label 'ubuntu-Appserver-3120'
            }
            steps
            {
                sh "docker compose down"
                sh "docker-compose up -d"
            }
        }
    }
}