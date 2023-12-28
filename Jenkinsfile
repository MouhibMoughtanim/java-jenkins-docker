pipeline {
    agent any

    environment {
        registry = "mouhibmoughtanim/java-jenkins-docker"
        registryCredential = 'dockerhub_id'
        dockerImage=''
    }

    tools {
        maven 'maven-3.9.6'
    }

    stages {
        stage('Build') {
            steps {
                sh 'mvn -B -DskipTests clean package'
            }
        }

        stage('Test') {
            steps {
                sh 'mvn test'
            }
            post {
                always {
                    junit 'target/surefire-reports/*.xml'
                }
            }
        }

        stage('Building our image') {
            steps {
                script {
                    dockerImage = docker.build registry + ":$BUILD_NUMBER"
                }
            }
        }

        stage('Deploy our image') {
            steps {
                script {
                    docker.withRegistry('', registryCredential) {
                        dockerImage.push()
                    }
                }
            }
        }

        stage('Run Ansible Playbook') {
       
            steps {
                script {
                    sh 'ansible-playbook /ansible/playbooks/playbook.yml'
                }
            }
        }
    }
}
