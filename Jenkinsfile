pipeline {
    agent any

    stages {
        stage('Cleanup old containers') {
            steps {
                script {
                    echo "Удаляем старые контейнеры..."
                    sh 'docker rm -f bek || true'
                    sh 'docker rm -f front || true'
                    sh 'docker rm -f sql111 || true'
                }
            }
        }

        stage('Create Database') {
            steps {
                script {
                    echo "Запускаем MSSQL Server..."
                    sh '''
                    docker run -d \
                        --name sql111 \
                        --hostname sql1 \
                        -e "ACCEPT_EULA=Y" \
                        -e "MSSQL_SA_PASSWORD=Qwerty-1" \
                        -p 1433:1433 \
                        mcr.microsoft.com/mssql/server:2022-latest
                    '''
                    sleep 10  // Даем время на запуск базы
                    sh 'docker ps | grep sql111'
                }
            }
        }

        stage('Docker Build') {
            steps {
                script {
                    echo "Собираем образы..."
                    sh 'docker build -t bek /var/lib/jenkins/workspace/Diplom_moon/BackEnd/Amazon-clone/'
                    sh 'docker build -t front /var/lib/jenkins/workspace/Diplom_moon/FrontEnd/my-app/'
                }
            }
        }

        stage('Check Images Before Push') {
            steps {
                script {
                    echo "Проверяем, какие образы есть перед пушем..."
                    sh 'docker images'
                }
            }
        }

        stage('Docker Login') {
            steps {
                script {
                    echo "Вход в Docker Hub..."
                    withCredentials([usernamePassword(credentialsId: '230d16c7-5b58-4f6e-b907-4e454834e110', usernameVariable: 'DOCKER_USERNAME', passwordVariable: 'DOCKER_PASSWORD')]) {
                        sh 'echo $DOCKER_PASSWORD | docker login -u $DOCKER_USERNAME --password-stdin'
                    }
                }
            }
        }

        stage('Docker Push') {
            steps {
                script {
                    echo "Тегируем и пушим образы..."
                    
                    sh 'docker tag bek moonyrise/monitor:bek'
                    sh 'docker push moonyrise/monitor:bek'

                    sh 'docker tag front moonyrise/monitor:front'
                    sh 'docker push moonyrise/monitor:front'
                }
            }
        }

        stage('Run Containers') {
            steps {
                script {
                    echo "Запускаем контейнеры..."
                    sh 'docker run -d --name bek -p 5034:5034 moonyrise/monitor:bek'
                    sh 'docker run -d --name front -p 81:80 moonyrise/monitor:front'
                }
            }
        }
    }
}
