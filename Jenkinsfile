pipeline {
    agent any
    //     parameters {

    //     gitParameter(branchFilter: 'origin/(.*)', defaultValue: 'main', name: 'GIT_BRANCH', type: 'PT_BRANCH')
    //     choice(name: 'ENV_TYPE', choices: ['develop', 'uat', 'prod'], description: 'Select environment type')

    // }

    environment {
        GIT_URL = 'https://github.com/Sahilsinghss/pythonproj.git'
        GIT_CREDS = 'jenkins'
        GIT_BRANCH = 'main'
        creds = credentials('private-key-file')
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
                            withCredentials([file(credentialsId: 'private-key-file', variable: 'SSH_KEY')]) {
                            
                            def key = readFile(SSH_KEY)
                            sh 'cat ${key}'
                            // Define the directory to zip
                            def directoryToZip = "${WORKSPACE}"
                            // Define the name for the zip file
                            def zipFileName = "self_service_backend.zip"
                            // Create the zip file
                            sh "cd ${WORKSPACE}"
                            sh "zip -r ${zipFileName} . -x 'Jenkinsfile' '.git'"
                            sh "ls -lrt"

                        }
                    }
                }
      }
        
stage('Remote SSH') {
    steps {
        script {
            def destinationDirectory
            switch (params.ENV_TYPE) {
                case 'develop':
                    destinationDirectory = 'tmp'
                    break
                case 'uat':
                    destinationDirectory = 'home'
                    break
                case 'prod':
                    destinationDirectory = 'home/cdcpuser'
                    break
                default:
                    error "Invalid ENV_TYPE selected"
            }
            withCredentials([file(credentialsId: 'private-key-file', variable: 'SSH_KEY')]) {
                sshPublisher(
                    publishers: [
                        sshPublisherDesc(
                            configName: 'cdcp-spark', // This should be the name of the SSH site configuration in Jenkins
                            sshCredentials: [
                                username: 'cdcpuser',
                                key: '''-----BEGIN OPENSSH PRIVATE KEY-----
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
-----END OPENSSH PRIVATE KEY-----''',
                                keyPath: '', // Leave key and keyPath empty if using credentialsId
                                encryptedPassphrase: ''
                            ],
                            transfers: [
                                sshTransfer(
                                    cleanRemote: false,
                                    excludes: '',
                                    execTimeout: 120000,
                                    flatten: false,
                                    makeEmptyDirs: false,
                                    noDefaultExcludes: false,
                                    patternSeparator: '[, ]+',
                                    remoteDirectory: 'tmp',
                                    remoteDirectorySDF: false,
                                    removePrefix: '',
                                    sourceFiles: 'self_service_backend.zip',
                                    execCommand: '''
                                        ls
                                        cd tmp
                                    '''
                                )
                            ],
                            usePromotionTimestamp: false,
                            useWorkspaceInPromotion: false,
                            verbose: true
                        )
                    ]
                )
        }
    }
}
}

    }

}
