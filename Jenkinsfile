pipeline {
    agent any
    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }
        stage('Run Ansible Playbook') {
            steps {
                script {
                    def env = env.BRANCH_NAME
                    def inventory = env == 'dev' ? 'dev' : env == 'staging' ? 'staging' : 'main'
                    ansiblePlaybook(
                        playbook: 'ansible/main.yml',
                        inventory: 'ansible/inventory.ini',
                        extras: "-e 'target=${inventory}'"
                    )
                }
            }
        }
    }
    post {
        always {
            script {
                githubNotify(
                    context: 'continuous-integration/jenkins',
                    description: 'Ansible Pipeline',
                    status: currentBuild.currentResult
                )
            }
        }
    }
}