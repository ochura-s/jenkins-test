pipeline {
    agent { label 'rocky-agent' }

    stages {
        stage('Check Environment') {
            steps {
                sh 'java -version'
                sh 'os-release'
            }
        }
        stage('Install Apache') {
            steps {
                sh '''
                    sudo dnf install -y httpd
                    sudo systemctl enable --now httpd
                    sudo firewall-cmd --permanent --add-service=http
                    sudo firewall-cmd --reload
                '''
            }
        }
        stage('Deploy Webpage') {
            steps {
                sh 'echo "<h1>Hello from Jenkins on Rocky Linux!</h1>" | sudo tee /var/www/html/index.html'
            }
        }
    }
}