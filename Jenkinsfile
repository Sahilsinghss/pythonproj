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
                    withCredentials([string(credentialsId: 'id-rsa-test', variable: 'SSH_KEY_FILE')])  {
                        def key = '''$SSH_KEY_FILE'''
                        sh 'cat key'
                    }
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
                                        key: '''$SSH_KEY_FILE''',
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
