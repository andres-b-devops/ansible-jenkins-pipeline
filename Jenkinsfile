pipeline {
  agent any
  environment { INVENTORY = "ansible/inventory.ini" }
  stages {
    stage('Checkout'){ steps { checkout scm } }
    stage('Run Ansible') {
      steps {
        sshagent(credentials: ['ansible-ec2-key']) {
          sh '''
              # Configurar variables de entorno para Ansible
              export ANSIBLE_HOST_KEY_CHECKING=False
              export ANSIBLE_TIMEOUT=30
              
              # Copiar la clave SSH a una ubicación temporal
              cp $SSH_AUTH_SOCK /tmp/ec2-key.pem || echo "SSH_AUTH_SOCK not available, using sshagent"
              
              # Ejecutar Ansible
              ansible-playbook -i ansible/inventory.ini ansible/main.yml -v
          '''
        }
      }
    }  
  }
}