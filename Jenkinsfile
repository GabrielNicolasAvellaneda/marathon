#!/usr/bin/env groovy

// General guidance: Use the pipeline syntax helper to write this, generally not a great idea to do by hand.
properties([buildDiscarder(logRotator(artifactDaysToKeepStr: '', artifactNumToKeepStr: '', daysToKeepStr: '10', numToKeepStr: '')),
    [$class: 'GitLabConnectionProperty', gitLabConnection: ''],
    [$class: 'RebuildSettings', autoRebuild: false, rebuildDisabled: false],
    parameters([string(defaultValue: 'master', description: 'Branch to locate marathon.groovy from. This facilitates testing changes to the pipeline.', name: 'GROOVY_BRANCH'),
        string(defaultValue: '', description: 'Phabricator Revision, e.g. D730 => 730, required for Phabricator and Submit Builds. Filled in automatically by Phabricator and bin/submit-patch.', name: 'REVISION_ID'),
        string(defaultValue: '', description: 'Phabricator Harbormaster object ID, required for Phabricator Builds. Filled in automatically by phabricator builds.', name: 'PHID'),
        string(defaultValue: '', description: 'Diff ID to build (which diff of D730, for example), required for Phabricator Builds. Filled in automatically by phabricator builds.', name: 'DIFF_ID'),
        string(defaultValue: '', description: 'Branch to land on (required for submit builds)', name: 'TARGET_BRANCH'),
        string(defaultValue: '', description: 'Release tag, e.g. v1.5.0-RC1. Only required for release-tag builds.', name: 'RELEASE_TAG'),
        string(defaultValue: '', description: 'Commit hash to tag. Only required for release-tag builds. The commit must be on a release branch.', name: "RELEASE_COMMIT"),
        booleanParam(defaultValue: false, description: 'Publish a snapshot to S3. Always true for master/release, enable explicitly for phabricator builds.', name: 'PUBLISH_SNAPSHOT')]),
    pipelineTriggers([])])

def m

ansiColor('gnome-terminal') {
  node('JenkinsMarathonCI-Debian8-2017-04-27') {
    // fetch the file directly from SCM so the job can use it to checkout the rest of the pipeline.
    deleteDir()
    checkout changelog: false, poll: false, scm: [$class: 'GitSCM', branches: [[name: GROOVY_BRANCH]], doGenerateSubmoduleConfigurations: false, extensions: [[$class: 'SparseCheckoutPaths', sparseCheckoutPaths: [[path: 'marathon.groovy']]]], submoduleCfg: [], userRemoteConfigs: [[credentialsId: 'mesosphere-ci-github', url: 'git@github.com:mesosphere/marathon.git']]]
    m = load("marathon.groovy")
    m.build_marathon()
  }
}
