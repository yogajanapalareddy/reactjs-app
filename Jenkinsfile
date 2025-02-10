pipeline {
    agent {
        node {
            label 'Jenkins_Agent'
        }
    }
        
    environment {
        ARTIFACT_DIR = "/var/lib/jenkins/artifacts"
        PLAYBOOK_FILE = "/home/ansimast/React_App_Test/my-app/ansible-playbooks/deploy.yml"
        INVENTORY_FILE = "/etc/ansible/hosts"
    }

    stages {
        stage('Checkout') {
            steps {
               script {
                    def branch = env.BRANCH_NAME
                    if (branch == 'feature-*') {
                        echo "Triggering deployment for feature-branch"
                    } else {
                        echo "Skipping build"
                        currentBuild.result = 'ABORTED'
                    }
                }
            }
        }        
        stage('Build Code') {
            steps {
                echo 'Building ReactJS code...'
                sh '''
                pwd
                npm install
                pwd
                npm run build
                mkdir -p ${ARTIFACT_DIR}
                pwd
                tar -czf ${ARTIFACT_DIR}/reactjs.tar.gz dist
                '''
            }
        }
        
        stage('Deploy Artifact') {
            agent {
                node {
                    label 'AnsibleMaster_Agent'
                }
            }
            steps {
                script {
                    echo 'Deploying ReactJS application to NGINX using Ansible...'
                    
                    // Copy artifact to Ansible machine (if not already there)
                    // Execute Ansible playbooks
                    sh """
                    sudo ansible-playbook ${PLAYBOOK_FILE} -i ${INVENTORY_FILE}
                    """
                }
            }
        }
    }
}

