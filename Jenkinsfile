node('ubuntu-Appserver-3120')
{
def app
stage('Cloning Git')
{
    /* Lets make sure we have the repo cloned to our workspace */
    checkout scm
}
stage('Build-and-Tag')
{
    /* Builds the actual image; synchronous to docker build on the CLI */
    app = docker.build('lkraimer/car_docker_repo')
}
stage('Post-to-DockerHub')
{
    /* Pushes to DockerHub! */
    docker.withRegistry('https://registry.hub.docker.com', 'dockerhub_credentials')
    {
        app.push('latest')
    }
}
stage('Deploy')
{
    sh "docker compose down"
    sh "docker-compose up -d"
}
}