pipeline {
    agent { label 'rocky-agent' }

    stages {
        stage('Check Environment') {
            steps {
                sh 'java -version'
                sh 'cat /etc/os-release'
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
                sh 'echo "<h1>Hello from Jenkins on Rocky Linux 111!</h1>" | sudo tee /var/www/html/index.html'
            }
        }
        stage('Check Logs for HTTP Errors') {
            steps {
                sh '''
                    LOG_FILE="/var/log/httpd/access_log"

                    if [ ! -f "$LOG_FILE" ]; then
                        echo "Log file $LOG_FILE does not exist yet."
                        exit 0
                    fi

                    echo "=== Checking $LOG_FILE for 4xx and 5xx errors ==="
                    echo ""

                    ERRORS_4XX=$(grep -E '" [4][0-9]{2} ' "$LOG_FILE" || true)
                    ERRORS_5XX=$(grep -E '" [5][0-9]{2} ' "$LOG_FILE" || true)

                    if [ -n "$ERRORS_4XX" ]; then
                        echo "--- 4xx Client Errors ---"
                        echo "$ERRORS_4XX"
                        echo ""
                        echo "4xx error count: $(echo "$ERRORS_4XX" | wc -l)"
                    else
                        echo "No 4xx errors found."
                    fi

                    echo ""

                    if [ -n "$ERRORS_5XX" ]; then
                        echo "--- 5xx Server Errors ---"
                        echo "$ERRORS_5XX"
                        echo ""
                        echo "5xx error count: $(echo "$ERRORS_5XX" | wc -l)"
                    else
                        echo "No 5xx errors found."
                    fi

                    if [ -n "$ERRORS_4XX" ] || [ -n "$ERRORS_5XX" ]; then
                        echo ""
                        echo "WARNING: HTTP errors detected in logs!"
                        exit 1
                    fi
                '''
            }
        }
    }
}