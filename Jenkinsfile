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
//     stage('Create Zip') {
//             steps {
//                 script {
//                     // Define the directory to zip
//                     def directoryToZip = "/var/lib/jenkins/workspace/python"
                    
//                     // Define the name for the zip file
//                     def zipFileName = "code.zip"
                    
//                     // Create the zip file
//                     //sh "zip -r ${zipFileName} ${directoryToZip}"
//                     archive(includes: "${directoryToZip}/**", excludes: '', allowEmptyArchive: true, excludesByFile: '') {
//     artifact zipFileName
// }

//                 }
//             }
//         }
//      stage('Remote SSH') {
//             steps {
//                 script {
//                     def remote = [:]
//                     remote.name = 'test'
//                     remote.host = '34.229.175.223'
//                     remote.user = 'jenkins'
//                     remote.password = 'Sahil123'
//                     remote.allowAnyHosts = true
                    
//                     // Write a script to a file
//                     //writeFile file: 'abc.sh', text: 'ls -lrt'
                    
//                     // Transfer the script file to the remote server
//                     sshPut remote: remote, from: '/var/lib/jenkins/workspace/python/code.zip', into: '/home/jenkins'
//                 }
//             }
//         }
        stage('Remote SSH') {
            steps {
                script {
                    sshPublisher(publishers: [sshPublisherDesc(configName: 'cdcp-spark', sshCredentials: [encryptedPassphrase: '{AQAAABAAAAAQ5tVoePFWpxHnFeh6dFlZqM4lYfZxr2BEZzV544Lr8gM=}', key: '', keyPath: '', username: 'cdcpuser'], sshLabel: [label: 'CDCP-SPARK'], transfers: [sshTransfer(cleanRemote: false, excludes: '', execCommand: 'ls', execTimeout: 120000, flatten: false, makeEmptyDirs: false, noDefaultExcludes: false, patternSeparator: '[, ]+', remoteDirectory: 'tmp', remoteDirectorySDF: false, removePrefix: '', sourceFiles: 'token.py')], usePromotionTimestamp: false, useWorkspaceInPromotion: false, verbose: true)])
                }
            }
        }
    }

}