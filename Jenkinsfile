pipeline {
    agent any

    environment {
        DEPLOY_USER = 'webserver1'
        DEPLOY_HOST = '192.168.30.53'
        DEPLOY_DIR  = '/var/www/html'
        NGINX_CONF  = '/etc/nginx/conf.d/test.conf'
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'master',
                    url: 'git@github.com:Unikoli/myhtml.git'
            }
        }

        stage('Build') {
            steps {
                echo "No build needed for static HTML"
            }
        }

        stage('Deploy') {
            steps {
                echo "Deploying files to web server..."
                // Copy files
                sh """
                rsync -avz --delete ./ ${DEPLOY_USER}@${DEPLOY_HOST}:${DEPLOY_DIR}/
                """

                // Create Nginx config & reload
                sh """
                ssh ${DEPLOY_USER}@${DEPLOY_HOST} '
                    echo "server {
                        listen 80;
                        server_name ${DEPLOY_HOST};

                        root ${DEPLOY_DIR};
                        index index.html;

                        location / {
                            try_files \\\$uri \\\$uri/ =404;
                        }
                    }" | sudo tee ${NGINX_CONF}

                    sudo nginx -t && sudo systemctl reload nginx
                '
                """
            }
        }
    }

    post {
        success {
            echo "✅ Deployment successful!"
        }
        failure {
            echo "❌ Deployment failed."
        }
    }
}

