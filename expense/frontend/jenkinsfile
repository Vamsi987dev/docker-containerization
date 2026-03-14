pipeline {
agent { label 'agent' }

options{
    timeout(time: 30, unit: 'MINUTES')
    disableConcurrentBuilds()
    //retry(1)
}

environment {
    AWS_REGION = 'us-east-1'
    ACCOUNT_ID = '222634367824'
    IMAGE_TAG = "${BUILD_NUMBER}"
    ECR_REPO = 'expense/dev/frontend'
}

stages {

    stage('Checkout Code') {
        steps {
            git branch: 'main', url: 'https://github.com/Vamsi987dev/docker-containerization.git'
        }
    }
    
    stage('Login to ECR') {
        steps {
            sh '''
            aws ecr get-login-password --region $AWS_REGION | \
            docker login --username AWS --password-stdin $ACCOUNT_ID.dkr.ecr.$AWS_REGION.amazonaws.com
            '''
        }
    }

    stage('Build Docker Image') {
        steps {
            sh '''
            docker build -t $ACCOUNT_ID.dkr.ecr.$AWS_REGION.amazonaws.com/$ECR_REPO:$IMAGE_TAG ./expense/frontend
            '''
        }
    }

    stage('Push Image to ECR') {
        steps {
            sh '''
            docker push $ACCOUNT_ID.dkr.ecr.$AWS_REGION.amazonaws.com/$ECR_REPO:$IMAGE_TAG
            '''
        }
    }
    // stage('Authenticate to EKS') {
    //     steps {
    //         sh '''
    //         aws eks update-kubeconfig \
    //         --region $AWS_REGION \
    //         --name expense-dev
    //         '''
    //     }
    // }
    // stage('Deploy to Kubernetes') {
    //     steps {
    //         sh '''
    //         helm upgrade --install frontend ./expense/frontend/helm \
    //         --namespace expense \
    //         --create-namespace \
    //         --set deployment.tag=$IMAGE_TAG \
    //         --wait --timeout 5m
    //         '''
    //     }
    // }

}

post {
    success {
        echo "Docker image built and pushed successfully!"
    }

    failure {
        echo "Pipeline failed. Check logs."
    }
}


}
