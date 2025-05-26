pipeline {
  agent any
  environment { 
      INVENTORY = "ansible/inventory.ini" 
      ANSIBLE_PRIVATE_KEY=credentials('ansible-ec2-key')
    }
  stages {
    stage('Checkout'){ steps { checkout scm } }
    stage('Run Ansible') {
      steps {
        sshagent(credentials: ['ansible-ec2-key']) {
          sh '''
              # Configurar variables de entorno para Ansible
              export ANSIBLE_HOST_KEY_CHECKING=False
              export ANSIBLE_TIMEOUT=30
              
              # Ejecutar Ansible
              ansible-playbook -i ansible/inventory.ini --private-key=${ANSIBLE_PRIVATE_KEY} ansible/main.yml -v
          '''
        }
      }
    }  
  }
}