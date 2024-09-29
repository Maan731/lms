pipeline {
    agent any

    stages {
        stage('LMS Code Analysis') {
            steps {
                echo 'Preparing Sonar Analysis'
                sh 'cd webapp && sudo docker run --rm -e SONAR_HOST_URL="http://3.25.106.64:9000" -v ".:/usr/src" -e SONAR_TOKEN="sqp_b2c1004fde906f0aa412ae900c40b60fab4b0416" sonarsource/sonar-scanner-cli -Dsonar.projectKey=lms'
                echo 'Completed Sonar Analysis'
            }
        }
        stage('LMS Build Artifacts') {
            steps {
                echo 'Preparing LMS Build'
                sh 'cd webapp && npm install && npm run build'
                echo 'Completed LMS Build'
            }
        }
        stage('LMS Release Artifacts') {
            steps {
                script {
                    echo 'Preparing LMS Release'
                    def packageJSON = readJSON file: 'webapp/package.json'
                    def packageJSONVersion = packageJSON.version
                    echo "${packageJSONVersion}"
                    sh "zip webapp/lms-${packageJSONVersion}.zip -r webapp/dist"
                    sh "curl -v -u admin:manunexus123 --upload-file webapp/lms-${packageJSONVersion}.zip http://3.25.106.64:8081/repository/lms/"
                    echo 'Completed LMS Release'
                }
            }
        }

        stage('LMS Deploy Artifacts') {
            steps {
                script {
                    echo 'Preparing LMS Deployment'
                    def packageJSON = readJSON file: 'webapp/package.json'
                    def packageJSONVersion = packageJSON.version
                    sh "curl -u admin:manunexus123 -X GET \'http://3.25.106.64:8081/repository/lms/lms-${packageJSONVersion}.zip\' --output lms-'${packageJSONVersion}'.zip"
                    sh 'sudo rm -rf /var/www/html/*'
                    sh "sudo unzip -o lms-'${packageJSONVersion}'.zip"
                    sh "sudo cp -r webapp/dist/* /var/www/html"
                    echo 'Completed LMS Deployment'
                }
            }
        }

        stage('LMS Clean Up') {
            steps {
                echo 'Cleaning Up'
                cleanWs()
            }
        }
    }
}