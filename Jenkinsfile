pipeline {
    agent any

    stages {
        stage ('Checkout code') {
            steps {
                git branch: 'master', url: 'https://github.com/sat1yam/Linux-patching-automation.git'
            }
        }
        stage ('Run Ansible Playbook') {
            steps {
                echo "Running Main Ansible Playbook for Patching"
		        sshagent(['satyam-ssh-key']) {
                	sh 'ansible-playbook -i inventory.ini main_patching.yml -u satyam'
		        }
            }
        }

    }
    post {
        success {
            echo "Patching is completed successfully."
        }
        failure {
            echo "patching failed. please check the logs."
        }
    }
}
