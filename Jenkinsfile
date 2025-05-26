pipeline {
  agent any
  environment { INVENTORY = "ansible/inventory.ini" }
  stages {
    stage('Checkout'){ steps { checkout scm } }
    stage('Run Ansible') {
      steps {
        sshagent(credentials: ['ansible_key']) {
          sh '''
              export ANSIBLE_HOST_KEY_CHECKING=False
              ansible-playbook -i ${INVENTORY} ansible/main.yml
          '''
        }
      }
    }  
  }
}