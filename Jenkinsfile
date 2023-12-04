pipeline {
    agent any
    stages {
        stage('Checkout Git') {
            steps {
                echo 'Pulling...'
                git branch: 'main', url: 'https://github.com/ImenSaadFkihAhmed/ProjetDevops.git'
            }
        }

        stage('Testing maven') {
            steps {
                sh 'mvn -version'
            }
        }

        stage('Mvn Clean') {
            steps {
                sh 'mvn clean'
            }
        }

        stage('Mvn Compile') {
            steps {
                sh 'mvn compile'
            }
        }

        stage('JUnit and Mockito Test') {
            steps {
                script {
                    sh 'docker run -d --name=mysql-container -e MYSQL_ROOT_PASSWORD=root -e MYSQL_DATABASE=tpachato -p 3306:3306 mysql:latest'
                    if (isUnix()) {
                        sh 'mvn --batch-mode test'
                    } else {
                        bat 'mvn --batch-mode test'
                    }
                    sh 'docker stop mysql-container'
                    sh 'docker rm mysql-container'
                }
            }
            post {
                always {
                    junit '**/target/surefire-reports/*.xml'
                }
            }
        }

        stage('SonarQube analysis 1') {
            steps {
                sh 'mvn sonar:sonar -Dsonar.login=admin -Dsonar.password=cheymouna'
            }
        }

        stage('Docker build') {
            steps {
                sh 'docker build -t imensaadfkihahmed/achat  .'
            }
        }

        stage('Docker login') {
            steps {
                sh 'echo $dockerhub_PSW | docker login -u imensaadfkihahmed -p cheymouna'
            }
        }

        stage('Docker hub Push') {
            steps {
                sh 'docker push imensaadfkihahmed/achat'
            }
        }

        stage('Run app With DockerCompose') {
            steps {
                sh 'docker-compose -f docker-compose.yml up -d'
            }
        }

        stage('Deploy to k8s') {
            steps {
                script {
                    kubernetesDeploy(configs: 'kubernetes.yaml', kubeconfigId: 'k8sconfigpwd')
                }
            }
        }
    }
}

