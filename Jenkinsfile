pipeline {
    agent any
    parameters {
        choice(name: 'PROJECT', choices: ['CRTLoader', 'ProjectB', 'ProjectC'], description: 'Select the project to build')
    }
    environment {
        REMOTE_USER = "Mickey"
        REMOTE_HOST = "10.3.10.110"
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
                    def projectPath = "${params.PROJECT}"
                    def solutionFile = "${projectPath}/${params.PROJECT}.sln"
                    
                    echo "Building ${params.PROJECT}..."
                    shell "msbuild ${solutionFile} /p:Configuration=Release"
                }
            }
        }

        stage('Transfer and Execute on Windows VM') {
            steps {
                withCredentials([sshUserPrivateKey(credentialsId: 'windows-ssh-key', keyFileVariable: 'CommandoVM')]) {
                    script {
                        def artifactPath = "${params.PROJECT}/bin/Release/${params.PROJECT}.exe"

                        // Secure SCP file transfer
                        shell """
                            scp -i $SSH_KEY ${artifactPath} ${REMOTE_USER}@${REMOTE_HOST}:C:\\Users\\Public\\malware.exe
                        """

                        // Secure SSH execution
                        shell """
                            ssh -i $SSH_KEY ${REMOTE_USER}@${REMOTE_HOST} "C:\\Users\\Public\\malware.exe"
                        """
                    }
                }
            }
        }
    }
}
