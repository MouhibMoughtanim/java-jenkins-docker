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
    agent {
        docker {
            image 'my-ansible-image'
            args '-v /var/run/docker.sock:/var/run/docker.sock'
             label 'docker'  
        }
    }
    steps {
        script {
            sh 'ansible-playbook /ansible/playbooks/playbook.yml'
        }
    }
}

     
    }
}
