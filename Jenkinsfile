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
                sh 'sudo docker build -t bek /var/lib/jenkins/workspace/Diplom_moon/BackEnd/Amazon-clone/'
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
                sh 'sudo docker build -t front /var/lib/jenkins/workspace/Diplom_moon/FrontEnd/my-app/'
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
                withCredentials([usernamePassword(credentialsId: '230d16c7-5b58-4f6e-b907-4e454834e110', usernameVariable: 'moonyrise', passwordVariable: 'dckr_pat_GV4m_gG3nVmdrBSpyoPHOcyAXH0')]) {
                    sh 'echo $DOCKER_PASSWORD | docker login -u $DOCKER_USERNAME --password-stdin'
                }
            }
	}
        stage('Docker push') {
            steps {
                script {
                    sh 'sudo docker tag bek moonyrise/monitor:bek'

                    sh 'sudo docker push moonyrise/monitor:bek'

                    sh 'sudo docker tag front moonyrise/monitor:front'

                    sh 'sudo docker push moonyrise/monitor:front'
                }
            }
        }
    }
}


