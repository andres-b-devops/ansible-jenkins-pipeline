pipeline {
  agent any
  environment { 
      INVENTORY = "ansible/inventory.ini" 
      ANSIBLE_PRIVATE_KEY=credentials('ansible-ec2-key')
    }
  stages {
    stage('Checkout'){ steps { checkout scm } }
    stage('Install Ansible collections'){
            steps {
                sh 'ansible-galaxy collection install -r ansible/requirements.yml'
            }
    }
    stage('Run Ansible') {
      steps {
        // Obtiene la ruta de la clave y el usuario
        withCredentials([sshUserPrivateKey(credentialsId: 'ansible-ec2-key', keyFileVariable: 'SSH_KEY', usernameVariable: 'SSH_USER')]) {
          sshagent(credentials: ['ansible-ec2-key']) {
            sh '''
              export ANSIBLE_HOST_KEY_CHECKING=False
              export ANSIBLE_TIMEOUT=30
              export ANSIBLE_SSH_PRIVATE_KEY_FILE=$SSH_KEY
              ansible-playbook -i ${INVENTORY} ansible/main.yml -u $SSH_USER
            '''
          }
        }
      }
    }  
  }
}