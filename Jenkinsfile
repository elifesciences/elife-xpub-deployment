elifePipeline {
    def commit
    stage 'Checkout', {
        checkout scm
        commit = elifeGitRevision()
    }

    stage 'Project tests', {
        lock('elife-xpub--ci') {
            builderDeployRevision 'elife-xpub--ci', commit
            builderSmokeTests 'elife-xpub--ci', '/srv/elife-xpub'
        }
    }

    elifeMainlineOnly {
        stage 'Deploy on end2end', {
            def elifeXpubCommit = sh(script: 'source .env && echo $XPUB_VERSION', returnStdout: true).trim()
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
