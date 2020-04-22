def getBranchName() { 
    if(env.CHANGE_ID != null) {
        return 'temp'
    } else {
        return 'master'
    }
}


timestamps {
    node(label: 'master') {
        stage('Checkout Git Repo') {
            git credentialsId: 'fe4effdc-f62d-4624-bcc7-d4749675f873',
            branch: getBranchName(),
            url: 'https://github.com/tilsharm-test-org/gh-pr-coverage.git'
        }
        stage('Test stage') {
            echo "test stage in progress"
        }
        stage('Archive and Record Tests') {
            if (fileExists('output/coverage/jest/cobertura-coverage.xml')) {
                archiveArtifacts 'output/coverage/jest/cobertura-coverage.xml'
                cobertura coberturaReportFile: 'output/coverage/jest/cobertura-coverage.xml'
            }
            else {
                echo 'XML report were not created'
            }
        }
        stage('Record Coverage') {
            if (env.CHANGE_ID == null) {
            currentBuild.result = 'SUCCESS'
            step([$class: 'MasterCoverageAction', scmVars: [GIT_URL: 'https://github.com/tilsharm-test-org/gh-pr-coverage.git']])
            } 
            else if (env.CHANGE_ID != null) {
            currentBuild.result = 'SUCCESS'
            step([$class: 'CompareCoverageAction', publishResultAs: 'statusCheck', scmVars: [GIT_URL: 'https://github.com/tilsharm-test-org/gh-pr-coverage.git']])
            }
        }
        stage('Clean Workspace') {
            cleanWs notFailBuild: true
        }
    }
}

