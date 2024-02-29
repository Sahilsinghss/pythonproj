pipeline {
    agent any

    environment {
        GIT_URL = 'https://github.com/Sahilsinghss/pythonproj.git'
        GIT_CREDS = 'jenkins'
        GIT_BRANCH = 'main'
    }

    stages {
        stage('Git Clone') {
            steps {
                script {
                    // Clean workspace before cloning (optional)
                    deleteDir()
                    
                    // Git clone
                    checkout([$class: 'GitSCM', branches: [[name: '*/main']], doGenerateSubmoduleConfigurations: false, extensions: [], submoduleCfg: [], userRemoteConfigs: [[url: GIT_URL,credentialsId: GIT_CREDS]]])
                    echo "Cloned code is in directory: ${pwd()}"
                }
            }
        }
    stage('Create Zip') {
            steps {
                script {
                    // Define the directory to zip
                    def directoryToZip = "/var/lib/jenkins/workspace/python"
                    
                    // Define the name for the zip file
                    def zipFileName = "code.zip"
                    
                    // Create the zip file
                    sh "zip -r ${zipFileName} ${directoryToZip}"
                    
                }
            }
        }
     stage('Transfer Zip to Server') {
            steps {
                script {
                    // Define server details
                    def server = '172.31.92.4'
                    def user = 'jenkins'
                    def password = 'Sahil123'
                    def remoteDir = '/home/jenkins'
                    def localZipFile = '/var/lib/jenkins/workspace/python/code.zip'
                    
                    // Transfer the zip file to the server using SCP
                    sh "/usr/bin/sshpass -p '${password}' scp ${localZipFile} ${user}@${server}:${remoteDir}"
                }
            }
        }
     // stage('Remote SSH') {
     //        steps {
     //            script {
     //                def remote = [:]
     //                remote.name = 'test'
     //                remote.host = '172.31.92.4'
     //                remote.user = 'jenkins'
     //                remote.keyFile = '/var/lib/jenkins/.ssh/id_rsa'
     //                remote.allowAnyHosts = true
                    
     //                // Write a script to a file
     //                //writeFile file: 'abc.sh', text: 'ls -lrt'
                    
     //                // Transfer the script file to the remote server
     //                sshPut remote: remote, from: '/var/lib/jenkins/workspace/python/code.zip', into: '/home/jenkins'
     //            }
     //        }
     //    }




    
    }
}
