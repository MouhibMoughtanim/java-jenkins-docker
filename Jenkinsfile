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
                    // Set ANSIBLE_LOCAL_TEMP to a writable directory within the workspace
                    withEnv(['ANSIBLE_LOCAL_TEMP=.ansible/tmp']) {
                        // Run the Docker container in privileged mode
                        docker.image('mouhibmoughtanim/devops_ansible:latest').inside("-v /home/mouhib/Desktop:/ansible/playbooks -v /var/jenkins_home/workspace/SmartIrrigationPipeline:/workspace --privileged") {
                            sh '/usr/bin/ansible-playbook /ansible/playbooks/playbook.yml'
                        }
                    }
                }
            }
        }
    }
}

