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
            //builderProjectTests 'elife-xpub--ci', '/srv/elife-xpub'
        }
    }

    elifeMainlineOnly {
        stage 'Deploy on end2end', {
            elifeSpectrum(
                deploy: [
                    stackname: 'elife-xpub--end2end',
                    revision: commit,
                    folder: '/srv/elife-xpub',
                    concurrency: 'blue-green'
                ],
                marker: 'xpub'
            )
        }

        stage 'Deploy on staging', {
            lock('elife-xpub--staging') {
                builderDeployRevision 'elife-xpub--staging', commit
                builderSmokeTests 'elife-xpub--staging', '/srv/elife-xpub'
            }
        }

        stage 'Approval', {
            elifeGitMoveToBranch commit, 'approved'
        }
    }
}
