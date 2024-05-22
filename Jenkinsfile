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
                            configName: 'dep-kube', // This should be the name of the SSH site configuration in Jenkins
                            sshCredentials: [
                                username: 'azureuser',
                                key: '''-----BEGIN RSA PRIVATE KEY-----
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
-----END RSA PRIVATE KEY-----''',
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
