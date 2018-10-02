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

        // TODO: doesn't exist yet
        //stage 'Deploy on end2end', {
        //    elifeGitMoveToBranch commit, 'master'
        //    builderDeployRevision 'elife-xpub--end2end', commit
        //}

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
