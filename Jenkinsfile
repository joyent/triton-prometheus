/*
 * This Source Code Form is subject to the terms of the Mozilla Public
 * License, v. 2.0. If a copy of the MPL was not distributed with this
 * file, You can obtain one at http://mozilla.org/MPL/2.0/.
 */

/*
 * Copyright 2020 Joyent, Inc.
 */

@Library('jenkins-joylib@v1.0.6') _

pipeline {

    agent {
        label joyCommonLabels(image_ver: '18.4.0')
    }

    options {
        buildDiscarder(logRotator(numToKeepStr: '30'))
        timestamps()
    }

    stages {
        stage('check') {
            steps{
                sh('make check')
            }
        }
        // avoid bundling devDependencies
        stage('re-clean') {
            steps {
                sh('git clean -fdx')
            }
        }
        stage('build image and upload') {
            steps {
                sh('''
export GO_ENV="$GO_ENV HOME=$JENKINS_HOME";
export ENGBLD_BITS_UPLOAD_IMGAPI=true;
make print-BRANCH print-STAMP all release publish buildimage bits-upload;
''')
            }
        }
    }

    post {
        always {
            joyMattermostNotification(channel: 'jenkins')
        }
    }

}
