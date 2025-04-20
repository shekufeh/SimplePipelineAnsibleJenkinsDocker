pipeline{
    agent none
    stages {
        stage('build'){
            agent{
                docker{
                    image 'maven:3.9.6-eclipse-temurin-17'
                    label 'docker-agent'
                    args '-v /root/.m2:/root/.m2'
                }
            }
            steps{
                sh 'mvn -X -U clean install'
                stash name: 'warfile', includes: 'target/*.war'
            }
        }
        stage('copy-file-to-ansible'){
            agent { label 'master' }
            steps{
                unstash 'warfile'
                sshagent(['SSH_ANSIBLE_JENKINS_CREDENTIALS_ID']){
                    sh """
                       ssh -o StrictHostKeyChecking=no ANSIBLE_USER@ANSIBLE_IP 'mkdir -p /tmp/deploy'
                       scp target/*.war ANSIBLE_USER@ANSIBLE_IP:/tmp/deploy/
                       scp deploy.yml ANSIBLE_USER@ANSIBLE_IP:/tmp/deploy/
                       scp hosts ANSIBLE_USER@ANSIBLE_IP:/tmp/deploy/
                       """
                }
            }
        }
        stage('Run Ansible Playbook') {
            agent { label 'master' }
            steps{
               sshagent(['SSH_ANSIBLE_JENKINS_CREDENTIALS_ID']){
                   sh """
                   ssh -o StrictHostKeyChecking=no ANSIBLE_USER@ANSIBLE_IP 'cd /tmp/deploy/'
                   ssh -o StrictHostKeyChecking=no ANSIBLE_USER@ANSIBLE_IP 'ansible-playbook -i /tmp/deploy/hosts /tmp/deploy/deploy.yml'
                   """
               }
            }
        }
    }
}
