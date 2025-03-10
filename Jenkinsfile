pipeline {
    agent { 
        dockerfile {
            args '-v /var/run/docker.sock:/var/run/docker.sock'
        }
    }

    environment{
        old_files = fileExists './output'
        old_containers = sh (script: 'docker ps -a -f name=cura-build-environment | wc -l', , returnStdout:true).trim()
    }

    stages {
        
        stage ('Clean environment files') {            

            when { expression { old_files == 'true' } }
            steps {
                sh 'rm -r ./output'
                echo old_containers
            }
        }

        stage ('Clean environment docker') {            

            when { expression { old_containers == "2" } }
            steps {
                sh 'docker stop cura-build-environment'
                sh 'docker rm cura-build-environment'
                sh 'docker image rm 8b25c9f4b47a'
            }
        }    

        stage ('Run build') {
            steps {
                sh 'pwd'
                sh 'find / | grep build.sh'
                sh 'ls -lah'
                sh 'ls -lah /etc/passwd'
                sh 'whoami'
                sh 'groups'
                sh './docker/linux/build.sh'
            }
        }

        stage ('Signing') {
            steps {
                withCredentials([file(credentialsId: 'private_gpg', variable: 'GPG_PRIVATE_KEY')]) {
    
                    sh 'gpg --batch --import $GPG_PRIVATE_KEY'
                    sh 'gpg --detach-sig --armor ./output/appimages/Ultimaker_Cura-*.AppImage'
                    sh 'gpg --export -a --output ./output/appimages/public_key.asc'
                    sh './signing/sha1sum_gen.sh'
                }
            }
        }

    }
    
    post {
        always {
            archiveArtifacts artifacts: 'output/appimages/', fingerprint: true
        }
    }

}
