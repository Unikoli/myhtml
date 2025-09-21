pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                echo "🔄 Checking out code from GitHub"
                git branch: 'master',
                    url: 'git@github.com:Unikoli/myhtml.git',
                     
            }
        }

        stage('Build') {
            steps {
                echo "✅ Build stage for static HTML (no compilation needed)"
                sh 'ls -l' // optional: list files as a “build check”
            }
        }
    }

    post {
        success {
            echo "🎉 Build completed successfully!"
        }
        failure {
            echo "❌ Build failed. Check logs."
        }
    }
}

