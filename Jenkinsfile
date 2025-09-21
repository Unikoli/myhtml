pipeline {
    agent any

    environment {
        DEPLOY_USER = 'webserver1'              // SSH user on web server
        DEPLOY_HOST = '192.168.30.53'           // Nginx server IP
        DEPLOY_DIR  = '/var/www/html'    // Target directory
        NGINX_CONF  = '/etc/nginx/conf.d/myhtml.conf'
        CREDENTIALS = 'ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAACAQDeffSMxJcmQGixsoaoG19+FcbzwLSAbt0bCda8RmvqZ7Nx0gHQy/KWdpgYnR7wZ3oDw6Srlw879TrIKXWH6yvq8CWwb5pWpgeijxsBNskjFiWOnrRJpndq3QMta2Rol7brKM6DD+1tWUyZ3U3MTOjHUHKeT/d1xBBHCA3zwDBDlWfst8ZYzm7ari4WrDjFSAnvYffUemZRDIcDQ65bOEycpnLoTWPS8ID2BOW8/8PXo/ae99H0oQ8qxsVM4jssLBJvU9GwhlVEVsl5cK0/hDWM9R/O828S76V3vHtODeKQvhk0uIYxniXoOSl3xTpGO9WiRWP7EFsrfH2D8rmjjpmtDFhoVKjZnNv45PucMx9EW7uyjdM69JosKHYS63zM7DPO6vxEZPs11D5wHUB9JvR60VJJG2hQrG+zUUo4mTa2snWx8HHg16ts+eg3tkm6MrkFIj+KNX5px2e/zUfYdnCUzYgoStkgzDv1XwHRMSBFrYako6+LfUUv+UILaUCr7GcViqK67q8pN8AWgaAkQqgWXS9Xpq87DaYpCEA8zk25FpRKylO33MKin12FyuHxoj12aMP/IEpw+WWRGa7oxjnz+tsnWnC3dDmKzq08g7Ej1+bfZff0FMH9vnA6GK10eIByZzWRCc0pZC20UyBpGK6w7U85X59jRjek+km3Sz1luQ== uniqueoli16@gmail.com'     // Jenkins credentialsId
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'master',
                    url: 'git@github.com:Unikoli/myhtml.git',
                    credentialsId: 'ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQDP2CxeZp4ePPTrPsQgnN9leyUsWHBHJshoeMzNeHDJhIGgO0eXCn+W0JD4rBB/75kF+0KDgtnU+9W9/ZG/Ilpe8dkdXJu7PnX4+6EVWR+vrzWjSzMDBD/OXA8jf2YH4P+vc0lZUHFWmc+H/m8RCn1Oga3eqIBpbAefF1KItdWoG4I4esmgQMErj79PRiPzVz74JL/+ZoC7njNs/xDNTUXu5BETzeXPDBdCeAAGp7CBRSR01z89hs/406yaXio+LRecUGk0s1nguMAsnHY9lUmu/1zu0T/ZofpLOOgwx43DUMZJ0/UUNzPfkTO0QOiUxnBa52mXt50+9X1USaGgI5u1 root@jenkins.unique.local'
            }
        }

        stage('Build') {
            steps {
                echo "✅ No build needed for static HTML"
            }
        }

        stage('Deploy') {
            steps {
                echo "🚀 Deploying files to ${DEPLOY_HOST}"
                sshagent (credentials: [CREDENTIALS]) {
                    sh """
                        ssh -o StrictHostKeyChecking=no ${DEPLOY_USER}@${DEPLOY_HOST} 'mkdir -p ${DEPLOY_DIR}'
                        rsync -avz --delete ./ ${DEPLOY_USER}@${DEPLOY_HOST}:${DEPLOY_DIR}/
                    """
                }
            }
        }

        stage('Nginx Reload') {
            steps {
                sshagent (credentials: [CREDENTIALS]) {
                    sh """
                        ssh -o StrictHostKeyChecking=no ${DEPLOY_USER}@${DEPLOY_HOST} \\
                        "echo 'server {
                            listen 80;
                            server_name ${DEPLOY_HOST};

                            root ${DEPLOY_DIR};
                            index index.html;

                            location / {
                                try_files \\$uri \\$uri/ =404;
                            }
                        }' | sudo tee ${NGINX_CONF} && sudo nginx -t && sudo systemctl reload nginx"
                    """
                }
            }
        }
    }

    post {
        success {
            echo "✅ Deployment successful! Visit http://${DEPLOY_HOST}"
        }
        failure {
            echo "❌ Deployment failed. Check Jenkins logs."
        }
    }
}
