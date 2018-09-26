node('ci-slave-adc-2') {
    sh '''echo -e "FROM ubuntu:latest
                RUN apt  update
            CMD /bin/bash " > Dockerfile'''
    sh 'docker version'
    
    docker.withRegistry('https://<IAM ID>.dkr.ecr.us-east-1.amazonaws.com','ecr:us-east-1:<jenkins aws credentials ID>') {
        sh "mkdir -p .docker; cat /root/.dockercfg > $WORKSPACE/.docker/config.json"
        withEnv(['DOCKER_CONFIG=$WORKSPACE/.docker']) {
            withCredentials([[$class: 'AmazonWebServicesCredentialsBinding', credentialsId:"<jenkins aws credentials ID>"]]) {
                sh '''eval $(docker run --rm -t $(tty &>/dev/null && echo "-i") -e "AWS_ACCESS_KEY_ID=${AWS_ACCESS_KEY_ID}" -e "AWS_SECRET_ACCESS_KEY=${AWS_SECRET_ACCESS_KEY}" -e "AWS_DEFAULT_REGION=us-east-1" mesosphere/aws-cli ecr get-login --no-include-email | sed 's|\\r||')'''
            }
            def customImage=docker.build("<ecr repo name>:<your tag>")
            customImage.push()
        }
    }   
}
