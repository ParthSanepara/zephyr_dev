pipeline {
    agent any

    parameters {
        string(name: 'NCS_VERSION', defaultValue: 'v2.6.2', description: 'NCS SDK version (e.g., v2.6.2)')
        string(name: 'BOARD', defaultValue: 'nrf52dk_nrf52832', description: 'Target board (e.g., nrf52dk_nrf52832)')
    }

    stages {
        stage('Checkout') {
            steps {
                checkout([$class: 'GitSCM', branches: [[name: '*/main']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/YOUR_GITHUB_USERNAME/YOUR_REPOSITORY.git']]])
            }
        }

        stage('Build') {
            agent {
                docker {
                    image 'ghcr.io/nrfconnect/sdk-nrf-toolchain:v2.6.99'
                    reuseNode true
                }
            }
            steps {
                sh '''
                    #!/bin/bash -xe
                    export PATH=$PATH:${HOME}/.local/bin
                    west init -l zephyr_dev
                    west update -o=--depth=1 -n
                    apt-get -y update
                    apt-get -y install build-essential
                    cd zephyr_dev
                    west build --build-dir build . --pristine -DBOARD_ROOT=. -DNCS_TOOLCHAIN_VERSION=NONE  -DCACHED_CONF_FILE=prj.conf --board ${params.BOARD}
                '''
            }
        }

        stage('Archive Artifacts') {
            steps {
                archiveArtifacts artifacts: 'zephyr_dev/build/zephyr/zephyr_dev.hex', fingerprint: true
            }
        }
    }
}
