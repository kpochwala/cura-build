pipeline {
    agent none
    stages {
        
        stage ('Clean environment') {
            agent { label 'jworker' }
            steps {
                sh 'rm -r output'
            }
        }

        stage ('Run build') {
            agent { label 'jworker' }
            steps {
                sh 'sudo ./docker/linux/build.sh'
            }
        }

    }
}
