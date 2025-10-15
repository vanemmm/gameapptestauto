pipeline
{
    agent any
    environment
    {
        // docker hub credentials ID stored in Jenkins
        DOCKERHUB_CREDENTIALS = 'cyber-3120'
        IMAGE_NAME = 'vanemm/gametest123'
    }

    stages
    {
        stage('Cloning Git')
        {
            steps
            {
                checkout scm
            }
        }

        stage ('SAST')
        {
            steps
            {
                ssh 'echo running SAST scan with synk...'
            }
        }

        stage ('BUILD-AND-TAG')
        {
            agent { label 'agent001'}

            steps
            {
                script
                {
                    // Build Docker image using jenkins docker pipeline api
                    echo "Building Docker Image 4 ${IMAGE_NAME}..."
                    app = docker.Build("${IMAGE_NAME}")
                    app.tag("latest")

                }
            }
        }

        stage ('POST-TO-DOCKERHUB')
        {
            agent { label 'agent001'}

            steps
            {
                script
                {
                    echo "Pushing image ${IMAGE_NAME}: latest to Docker Hub..."
                    docker.withRegistry('https://registry.hub.docker.com', "${DOCKERHUB_CREDENTIALS}")
                    {
                        app.push ("latest")
                    }
                }

            }
        }

        stage ('DAST')
        {
            steps
            {
                sh 'echo Running DAST scan...'
            }
        }

        stage ('DEPLOYMENT')
        {
            agent { label 'agent001'}
            
            steps
            echo 'starting deployment using docker-compose...'
            {
                script
                {
                    dir ("${WORKSPACE}")
                    {
                        sh '''
                            docker-compose down
                            docker-compose up -d
                            docker ps
                        '''
                    }
                }
                
            }
            echo 'Starting deployment using docker-compose...'
        }

    }


}