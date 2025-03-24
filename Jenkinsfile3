pipeline {
    agent any
    stages {
	    stage('Create database') {
            steps {
                script {
                    sh 'sudo docker rm sql111 --force'
                    sh 'sudo docker run -e "ACCEPT_EULA=Y" -e "MSSQL_SA_PASSWORD=Qwerty-1" -p 1433:1433 --name sql111 --hostname sql1 -d mcr.microsoft.com/mssql/server:2022-latest'
                    sh 'sleep 30'
                }
            }
        }
	    stage('Docker-build-bek') {
            steps {
                sh 'sudo docker build -t bek /var/lib/jenkins/workspace/site/BackEnd/Amazon-clone/'
            }
        }
        stage('docker run bek') {
            steps {
                sh 'sudo docker rm bek --force'
                sh 'sudo docker run -d --name bek -p 5034:5034 bek'
            }
        }
        stage('Docker-build-front') {
            steps {
                sh 'sudo docker build -t front /var/lib/jenkins/workspace/site/FrontEnd/my-app/'
            }
        }
        stage('docker run front') {
            steps {
                sh 'sudo docker rm front --force'
                sh 'sudo docker run -d --name front -p 81:80 front'
            }
        }
        stage("Docker login") {
            steps {
                echo " ============== docker login =================="
                withCredentials([usernamePassword(credentialsId: 'c357dd53-c106-43ab-bae3-67b806c17a87', usernameVariable: 'USERNAME', passwordVariable: 'PASSWORD')]) {
                    sh 'docker login -u $USERNAME -p $PASSWORD'
                }
            }
	}
        stage('Docker push') {
            steps {
                script {
                    sh 'sudo docker tag bek yurashupik/cloud:bek'

                    sh 'sudo docker push yurashupik/cloud:bek'

                    sh 'sudo docker tag front yurashupik/cloud:front'

                    sh 'sudo docker push yurashupik/cloud:front'
                }
            }
        }
    }
}


