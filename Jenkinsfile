pipeline {
    agent any
    environment {
        REMOTE_USER = "Mickey"
        REMOTE_HOST = "10.3.10.110"
	MALWARE = "CRTLoader"
    }
    stages {
        stage('Checkout Code') {
            steps {
                git branch: 'main', url: 'https://github.com/MickeyDB/MalTrash.git'
            }
        }

        stage('Build Project') {
            steps {
                script {
                    def projectPath = "${MALWARE}"
                    def solutionFile = "${projectPath}/${MALWARE}.sln"
                    
                    echo "Building ${MALWARE}..."
                    sh "/usr/local/share/dotnet/dotnet build ${solutionFile} /p:Configuration=Release"
                }
            }
        }

	stage('Verify Build Output') {
            steps {
                sh "ls -lah ${MALWARE}/bin/Release/"
            }
        }

        stage('Transfer and Execute on Windows VM') {
            steps {
                withCredentials([sshUserPrivateKey(credentialsId: 'CommandoVM', keyFileVariable: 'SSH_KEY')]) {
                    script {
                        def artifactPath = "${MALWARE}/bin/Release/${MALWARE}.exe"

                        // Secure SCP file transfer
                        sh '''
                            scp -i $SSH_KEY ${artifactPath} ${REMOTE_USER}@${REMOTE_HOST}:C:/Users/Public/not_malware.exe
                        '''

                        // Secure SSH execution
                        sh '''
                            ssh -i $SSH_KEY ${REMOTE_USER}@${REMOTE_HOST} "C:/Users/Public/not_malware.exe"
                        '''
                    }
                }
            }
        }
    }
}
