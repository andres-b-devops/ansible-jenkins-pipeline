pipeline {
  agent any
  environment { INVENTORY = "ansible/inventory.ini" }
  stages {
    stage('Checkout'){ steps { checkout scm } }
    stage('Test SSH Connection') {
        steps {
            container('ansible') {
                withCredentials([sshUserPrivateKey(
                    credentialsId: 'ansible-ec2-key',
                    keyFileVariable: 'SSH_PRIVATE_KEY',
                    usernameVariable: 'SSH_USERNAME'
                )]) {
                    sh '''
                        mkdir -p /home/jenkins/tmp
                        cp "$SSH_PRIVATE_KEY" /home/jenkins/tmp/ansible-ec2-key.pem
                        chmod 600 /home/jenkins/tmp/ansible-ec2-key.pem
                        
                        export ANSIBLE_HOST_KEY_CHECKING=False
                        ansible webservers -i ansible/inventory.ini -m ping -v
                    '''
                }
            }
        }
    }    
    stage('Run Ansible') {
    steps {
        container('ansible') {
            withCredentials([sshUserPrivateKey(
                credentialsId: 'ansible-ec2-key',  // Tu ID correcto
                keyFileVariable: 'SSH_PRIVATE_KEY',
                usernameVariable: 'SSH_USERNAME'
            )]) {
                sh '''
                    # Configurar variables de entorno
                    export ANSIBLE_HOST_KEY_CHECKING=False
                    export ANSIBLE_TIMEOUT=30
                    
                    # Crear directorios necesarios
                    mkdir -p /home/jenkins/.ssh
                    mkdir -p /home/jenkins/tmp
                    
                    # Copiar la clave a donde Ansible la busca
                    cp "$SSH_PRIVATE_KEY" /home/jenkins/tmp/ansible-ec2-key.pem
                    chmod 600 /home/jenkins/tmp/ansible-ec2-key.pem
                    
                    # Verificar que la clave existe
                    ls -la /home/jenkins/tmp/ansible-ec2-key.pem
                    
                    # Ejecutar Ansible
                    ansible-playbook -i ansible/inventory.ini ansible/main.yml -v
                '''
            }
        }
      }
    } 
  }
}