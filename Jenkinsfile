pipeline {
    environment {
        registry = "mouhibmoughtanim/java-jenkins-docker"
        registryCredential = 'dockerhub_id'
        dockerImage=''
    }
    agent any

    tools {
        maven 'maven-3.9.6'
    }

    stages {
        stage('Build') {
            steps {
                script {
                    sh 'mvn -B -DskipTests clean package'
                }
            }
        }

        stage('Test') {
            steps {
                script {
                    sh 'mvn test'
                }
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
                    ansiblePlaybook playbook: '/ansible/playbooks/playbook.yml'
                }
            }
        }
     
    }
}
