node {

   stage 'Checkout'
   git 'https://github.com/omarlari/aws-container-sample-app.git'

   stage 'Build Docker image'
   docker.build('hello')

   stage 'Push to registry'
   sh ("eval \$(docker run awscli aws ecr get-login --region ${REGION} --no-include-email | sed 's|https://||')")
   docker.withRegistry('https://${ECR_REPO}') {
       docker.image('hello').push('${BUILD_NUMBER}')
   }

   stage 'Update application'

   kubernetes: { node {
   docker.image('kubectl').inside("--volume=/home/ec2-user/.kube:/config/.kube"){
       sh 'kubectl describe deployment ${APP}'
       sh 'kubectl set image deployment/${APP} hello=${ECR_REPO}/hello:${BUILD_NUMBER}'
       sh 'kubectl describe deployment ${APP}'
       }
   }}
}
