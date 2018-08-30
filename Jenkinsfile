pipeline
{
    agent any
    environment 
    {
        VERSION = 'latest'
        PROJECT = 'sample'
        IMAGE = 'sample:latest'
    }
    stages
    {
        stage('Build Preparations')
        {
            steps
            {
                checkout scm
            }
        }
        stage('Docker Build')
        {
            steps
            {
                script
                {   
                    IMAGE = "${params.imagename}"
                    docker.build("$IMAGE")
                    sh """
                      echo "$IMAGE ${WORKSPACE}/Dockerfile " > anchore_images
                    """
                }
            }
        }
        stage('Docker Scan')
        {
            steps
            {
                script
                {   
                     docker.image('anchore/jenkins')
                     anchore bundleFileOverride: 'anchore_policies.json', inputQueries: [[query: 'cve-scan all'], [query: 'list-packages all'], [query: 'list-files all'], [query: 'show-pkg-diffs base']], name: 'anchore_images', policyEvalMethod: 'bundlefile'
                }
            }
        }
    }
    post
    {
        always
        {
            //sh "docker rmi $IMAGE | true"
            docker images
        }
    }
}
