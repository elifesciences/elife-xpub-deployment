elifePipeline {
    def commit
    stage 'Checkout', {
        checkout scm
        commit = elifeGitRevision()
    }

    stage 'Smoke tests', {
        node('containers-jenkins-plugin') {
            checkout scm
            withCommitStatus({
                try {
                    sh 'docker-compose up -d'
                    sh 'docker wait xpub_bootstrap_1'
                    sh 'curl --verbose --fail localhost:3000'
                } finally {
                    sh 'docker-compose down -v'
                }
            }, 'ci/smoke-tests', commit)
        }
    }

    elifeMainlineOnly {
        stage 'Deploy on end2end', {
            def elifeXpubCommit = sh(script: "/bin/bash -c 'source .env && echo \$XPUB_VERSION'", returnStdout: true).trim()
            elifeSpectrum(
                deploy: [
                    stackname: 'elife-xpub--end2end',
                    revision: commit,
                    folder: '/srv/elife-xpub',
                    concurrency: 'blue-green',
                ],
                marker: 'xpub',
                commitStatus: [
                    repository: 'elifesciences/elife-xpub',
                    revision: elifeXpubCommit
                ]
            )
        }

        stage 'Deploy on staging', {
            lock('elife-xpub--staging') {
                builderDeployRevision 'elife-xpub--staging', commit, 'blue-green'
                builderSmokeTests 'elife-xpub--staging', '/srv/elife-xpub'
            }
        }

        stage 'Approval', {
            elifeGitMoveToBranch commit, 'approved'
        }
    }
}
