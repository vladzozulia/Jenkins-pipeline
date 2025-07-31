pipeline {
    agent {
        label 'apache-vm'
    }

    stages {
        stage('Checkout SCM') {
            steps {
                echo 'getting repo code...'
                checkout scm
            }
        }

        stage('Install Apache') {
            steps {
                script {
                    echo "installing apache agent..."
                    sh 'sudo apt-get update -y'
                    sh 'sudo apt-get install -y apache2'
                    sh 'sudo systemctl status apache2'
                }
            }
        }

        stage('Analyze Apache Logs') {
            steps {
                script {
                    echo "analize logfile 4xx та 5xx..."
                    def logFile = '/var/log/apache2/access.log'
                    def errorCheckScript = """
                        if [ -f ${logFile} ]; then
                            ERROR_COUNT=\$(sudo egrep ' \\"[45][0-9]{2}\\" ' ${logFile} | wc -l)
                            if [ "\$ERROR_COUNT" -gt "0" ]; then
                                echo "found \$ERROR_COUNT errors (4xx/5xx)."
                                sudo egrep ' \\"[45][0-9]{2}\\" ' ${logFile}
                            else
                                echo "errors 4xx/5xx not found."
                            fi
                        else
                            echo "logfile ${logFile} not found."
                        fi
                    """
                    sh(script: errorCheckScript)
                }
            }
        }
    }

    post {
        always {
            echo 'agent pipeline end.'
        }
    }
}
