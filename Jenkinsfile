pipeline {
    agent any

    environment {
        PROJECT_ID = "sylvan-hydra-464904-d9"
        REGION = "us-central1"
        REPO = "cloudrun-repo"
        SERVICE = "flask-app"
        IMAGE = "${REGION}-docker.pkg.dev/${PROJECT_ID}/${REPO}/${SERVICE}:${GIT_COMMIT}"
        GOOGLE_APPLICATION_CREDENTIALS = credentials('jenkins-sa-key')  // ID of secret file credential in Jenkins
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Auth to GCP') {
            steps {
                sh 'gcloud auth activate-service-account --key-file=$GOOGLE_APPLICATION_CREDENTIALS'
                sh 'gcloud config set project $PROJECT_ID'
            }
        }

        stage('Build & Push Image') {
            steps {
                sh "gcloud builds submit --tag ${IMAGE} ."
            }
        }

        stage('Deploy to Cloud Run') {
            steps {
                sh """
                gcloud run deploy ${SERVICE} \
                    --image ${IMAGE} \
                    --region ${REGION} \
                    --platform managed \
                    --allow-unauthenticated
                """
            }
        }
    }
}
