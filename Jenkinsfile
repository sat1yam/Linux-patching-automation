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
        stage('Collect Check Files') {
            steps {
                sh '''
                    mkdir -p checks
                    cp /tmp/precheck_*.txt checks/ || true
                    cp /tmp/postcheck_*.txt checks/ || true
                '''
            }
        }

    }
    post {
        success {
            sendEmail("SUCCESS")
        }
        failure {
            sendEmail("FAILURE")
        }
    }
}

def sendEmail(String STATUS) {
    def subjectMsg = (STATUS == "SUCCESS") ?
        "✅ Jenkins Job Successful: ${env.JOB_NAME}" :
        "❌ Jenkins Job Failed: ${env.JOB_NAME}"

    def color = (STATUS == "SUCCESS") ? "#2ecc71" : "#e74c3c"  // Green / Red

    emailext(
        subject: subjectMsg,
        to: "2021wb86549@wilp.bits-pilani.ac.in",
        replyTo: "2021wb86549@wilp.bits-pilani.ac.in",
        mimeType: "text/html",
        attachmentsPattern: 'checks/*.txt',
        body: """
<!DOCTYPE html>
<html>
<head>
<style>
    body { font-family: Arial, sans-serif; padding: 10px; }
    .header {
        padding: 15px;
        color: white;
        background-color: ${color};
        border-radius: 5px;
        font-size: 20px;
        font-weight: bold;
    }
    .content {
        margin-top: 20px;
        font-size: 14px;
    }
    table {
        width: 100%;
        border-collapse: collapse;
        margin-top: 15px;
    }
    td {
        padding: 8px;
        border-bottom: 1px solid #ddd;
    }
    .footer {
        margin-top: 20px;
        font-size: 12px;
        color: #777;
    }
</style>
</head>
<body>

<div class="header">
    Jenkins Build Status: ${STATUS}
</div>

<div class="content">
    <p>Hello Team,</p>

    <p>
        The Jenkins job <b>${env.JOB_NAME}</b> has completed with the following status:
    </p>

    <table>
        <tr><td><b>Job Name</b></td><td>${env.JOB_NAME}</td></tr>
        <tr><td><b>Build Number</b></td><td>${env.BUILD_NUMBER}</td></tr>
        <tr><td><b>Status</b></td><td><b>${STATUS}</b></td></tr>
        <tr><td><b>Build URL</b></td><td><a href="${env.BUILD_URL}">${env.BUILD_URL}</a></td></tr>
        <tr><td><b>Workspace</b></td><td>${env.WORKSPACE}</td></tr>
        <tr><td><b>Executed on Node</b></td><td>${env.NODE_NAME}</td></tr>
    </table>

    <p>Attached: <b>patching_output.log</b> (Ansible run output)</p>

    <p class="footer">
        This is an automated email sent by Jenkins.
    </p>
</div>

</body>
</html>
"""
    )
}