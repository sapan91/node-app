pipeline{
    agent any
        environment{
            EC2_HOST = "13.232.1.154"
            SSH_CREDENTIAL_ID = "ubuntu"
            REMOTE_USER = "ubuntu"
            REMOTE_PATH = "/home/ubuntu/app"
            WEB_ROOT = "/var/www/html"
            SERVER = "nginx"
        }
        stages{
            stage("Build"){
            echo "Installing Dependencies"
            sh "npm install"
            echo "Building the app"
            sh "npm run build"
            echo "Build complete"
            }
        }
        stage("Deploy"){
            steps{
                echo "Starting Deployment"
                sshagent(credential: [env.SSH_CREDENTIAL_ID])
                sh """
                    echo "Creating remote directory"
                    ssh -o StrictHostKeyChecking=no ${REMOTE_USER}@${EC2_HOST} 'mkdir -p ${REMOTE_PATH}'
                    echo "Copying Distribution to remote path"
                    scp -o StrictHostKeyChecking=no -r dist/* ${REMOTE_USER}@${EC2_HOST}:${REMOTE_PATH}/
                    echo "Restarting Ngnix"
                    ssh -o StrictHostKeyChecking=no ${REMOTE_USER}@${EC2_HOST}'
                    rm -rf ${WEB_ROOT}/*
                    sudo cp -r ${REMOTE_PATH}/* ${WEB_ROOT}/
                    sudo systemctl restart ${SERVER}
                ""
            }

        }

}   