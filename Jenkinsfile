pipeline {
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
            agent any
            steps {
                script {
                    sh 'mvn -B -DskipTests clean package'
                }
            }
        }

        stage('Test') {
                 agent any

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
             agent any

            steps {
                script {
                    dockerImage = docker.build registry + ":$BUILD_NUMBER"
                }
            }
        }

        stage('Deploy our image') {
             agent any
 
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
                dockerfile 'Dockerfile-ansible'
                args '-v /var/run/docker.sock:/var/run/docker.sock'
            }
            steps {
                script {
                    sh 'ansible-playbook /ansible/playbooks/playbook.yml'
                }
            }
        }
    }
}

