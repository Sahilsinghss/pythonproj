pipeline {
    agent any
        parameters {

        gitParameter(branchFilter: 'origin/(.*)', defaultValue: 'main', name: 'GIT_BRANCH', type: 'PT_BRANCH')
        choice(name: 'ENV_TYPE', choices: ['develop', 'uat', 'prod'], description: 'Select environment type')

    }

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
                    checkout([$class: 'GitSCM', branches: [[name: "${params.GIT_BRANCH}"]], doGenerateSubmoduleConfigurations: false, extensions: [], submoduleCfg: [], userRemoteConfigs: [[url: GIT_URL,credentialsId: GIT_CREDS]]])
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
                    //sh "zip -r ${zipFileName} ${directoryToZip}"
                    archive(includes: "${directoryToZip}/**", excludes: '', allowEmptyArchive: true, excludesByFile: '') {
    artifact zipFileName
}

                }
            }
        }
     // stage('Transfer Zip to Server') {
     //        steps {
     //            script {
     //                // Define server details
     //                def server = '54.242.201.51'
     //                def user = 'jenkins'
     //                def password = 'Sahil123'
     //                def remoteDir = '/home/jenkins'
     //                def localZipFile = '/var/lib/jenkins/workspace/python/code.zip'
                    
     //                // Transfer the zip file to the server using SCP
     //                sh "/usr/bin/sshpass -p '${password}' scp ${localZipFile} ${user}@${server}:${remoteDir}"
     //            }
     //        }
     //    }
     stage('Remote SSH') {
            steps {
                script {
                    def remote = [:]
                    remote.name = 'test'
                    remote.host = '34.229.175.223'
                    remote.user = 'jenkins'
                    remote.password = 'Sahil123'
                    remote.allowAnyHosts = true
                    
                    // Write a script to a file
                    //writeFile file: 'abc.sh', text: 'ls -lrt'
                    
                    // Transfer the script file to the remote server
                    sshPut remote: remote, from: '/var/lib/jenkins/workspace/python/code.zip', into: '/home/jenkins'
                }
            }
        }
        // stage('Deploy') {
        //     steps {
        //         script {
        //             // Transfer the zip file to the remote server
        //             sshPut remote: remoteServer('your-ssh-server-id'), sourceFiles: "${WORKSPACE}/my_code.zip", remoteDirectory: '/path/to/destination/on/remote/server/'

        //             // Unzip the file on the remote server and place its contents into desired folders
        //             sshScript remote: remoteServer('your-ssh-server-id'), script: '''
        //                 unzip -o /path/to/destination/on/remote/server/my_code.zip -d /path/to/destination/on/remote/server/
        //                 # Move the unzipped contents to desired folders
        //                 mv /path/to/destination/on/remote/server/<unzipped_folder_name>/* /desired/folder/path/
        //                 '''
        //         }
        //     }
        // }



    
    }
}
