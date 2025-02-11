pipeline {
    agent any

    // Environment Variables
    environment {
        MAJOR = '1'
        MINOR = '0'
        // Orchestrator Services
        UIPATH_ORCH_URL = "https://cloud.uipath.com/"
        UIPATH_ORCH_LOGICAL_NAME = "mouriorxmxcl"
        UIPATH_ORCH_TENANT_NAME = "DefaultTenant"
        UIPATH_ORCH_FOLDER_NAME = "Shared"
    }

    stages {
        // Printing Basic Information
        stage('Preparing') {
            steps {
                echo "Jenkins Home ${env.JENKINS_HOME}"
                echo "Jenkins URL ${env.JENKINS_URL}"
                echo "Jenkins JOB Number ${env.BUILD_NUMBER}"
                echo "Jenkins JOB Name ${env.JOB_NAME}"
                echo "GitHub BranchName ${env.BRANCH_NAME}"
                checkout scm
            }
        }

        // Build Stages
        stage('Build') {
            steps {
                echo "Building..with ${WORKSPACE}"
                sh """
                uipcli pack --output Output/${env.BUILD_NUMBER} --project project.json --version ${MAJOR}.${MINOR}.${env.BUILD_NUMBER}
                """
            }
        }

        // Test Stages
        stage('Test') {
            steps {
                echo 'Testing..the workflow...'
            }
        }

        // Deploy Stages
        stage('Deploy to UAT') {
            steps {
                echo "Deploying ${BRANCH_NAME} to UAT"
                sh """
                uipcli deploy --package Output/${env.BUILD_NUMBER} --orchestrator ${UIPATH_ORCH_URL} --tenant ${UIPATH_ORCH_TENANT_NAME} --folder ${UIPATH_ORCH_FOLDER_NAME} --environment DEV --account ${UIPATH_ORCH_LOGICAL_NAME} --credentials UiPathNazeer --entry-point Main.xaml --create-process
                """
            }
        }

        // Deploy to Production Step
        stage('Deploy to Production') {
            steps {
                echo 'Deploy to Production'
            }
        }
    }

    // Options
    options {
        // Timeout for pipeline
        timeout(time: 80, unit: 'MINUTES')
        skipDefaultCheckout()
    }

    post {
        success {
            echo 'Deployment has been completed!'
        }
        failure {
            echo "FAILED: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]' (${env.JOB_DISPLAY_URL})"
        }
        always {
            /* Clean workspace if success */
            cleanWs()
        }
    }
}
