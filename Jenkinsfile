pipeline {
    agent any
    environment {
        // Credenciales SSH para Ansible, obtenidas del almacén de credenciales de Jenkins
        ANSIBLE_PRIVATE_KEY = credentials('ansible-ec2-key')
        // Puedes mantener el INVENTORY aquí si es siempre el mismo
        INVENTORY = "ansible/inventory.ini"
    }
    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }
        stage('Install Ansible collections') {
            steps {
                sh 'ansible-galaxy collection install -r ansible/requirements.yml'
            }
        }
        stage('Run Ansible Playbook') {
            steps {
                script {
                    // Determinar el 'target' de Ansible basado en el nombre de la rama
                    def branchName = env.BRANCH_NAME
                    def ansibleTarget = branchName == 'dev' ? 'dev' : branchName == 'staging' ? 'staging' : 'main'

                    // Utiliza las credenciales SSH y pasa la clave y el usuario a Ansible
                    withCredentials([sshUserPrivateKey(credentialsId: 'ansible-ec2-key', keyFileVariable: 'SSH_KEY', usernameVariable: 'SSH_USER')]) {
                        sshagent(credentials: ['ansible-ec2-key']) {
                            sh """
                                export ANSIBLE_HOST_KEY_CHECKING=False
                                export ANSIBLE_TIMEOUT=30
                                export ANSIBLE_SSH_PRIVATE_KEY_FILE=$SSH_KEY
                                # Ejecuta el playbook de Ansible, pasando el target y el usuario SSH
                                ansible-playbook -i ${INVENTORY} ansible/main.yml -u $SSH_USER -e 'target=${ansibleTarget}'
                            """
                        }
                    }
                }
            }
        }
    }
    post {
        always {
            script {
                // Notificar a GitHub el estado del pipeline
                githubNotify(
                    context: 'continuous-integration/jenkins',
                    description: 'Ansible Pipeline',
                    status: currentBuild.currentResult,
                    targetUrl: "${env.BUILD_URL}"
                )
            }
        }
    }
}