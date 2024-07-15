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
        dir = 'test'
        VENV_DIR = 'venv'
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

        // stage('Setup Python Environment') {
        //     steps {
        //         script {
        //             // Install virtualenv if not installed

        //             // Create a virtual environment
        //             sh "python3 -m venv ${VENV_DIR}"

        //             // Activate the virtual environment and install dependencies
        //             sh """
        //             . ${VENV_DIR}/bin/activate
        //             pip install pytest
        //             """
        //         }
        //     }
        // }
 
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
                    def test = 'it is just a test'
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
                    withCredentials([string(credentialsId: 'id-rsa-test', variable: 'SSH_KEY_FILE')])  {
                        sshPublisher(
                            publishers: [
                                sshPublisherDesc(
                                    configName: 'cdcp-spark', // This should be the name of the SSH site configuration in Jenkins
                                    sshCredentials: [
                                        username: 'cdcpuser',
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
                                            remoteDirectory: destinationDirectory,
                                            remoteDirectorySDF: false,
                                            removePrefix: '',
                                            sourceFiles: 'self_service_backend.zip',
                                            execCommand: """
                                                echo $test
                                            """
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
