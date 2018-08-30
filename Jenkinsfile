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
                     anchore bundleFileOverride: 'anchore_policies.json', inputQueries: [[query: 'cve-scan all'], [query: 'list-packages all'], [query: 'list-files all'], [query: 'show-pkg-diffs base']], name: 'anchore_images', policyEvalMethod: 'bundlefile'
                }
            }
        }
        stage('Push Image')
        {
            when {
                branch 'master'
            }
            steps {
                withDockerRegistry([ credentialsId: "docker", url: "" ]) {
                    sh 'docker tag sample:latest saurabhjambhule/sample'
                    sh 'docker push saurabhjambhule/sample'
                }
            }
        }
    }
    post
    {
        always
        {
            script
                {   
                    sh """
                      echo "Done:)"
                    """
                }
        }
    }
}
