#!/usr/bin/env groovy

// useful info:
// https://github.com/jenkinsci/pipeline-github-plugin
// https://jenkins.traceair.net/jenkins/directive-generator/


void setBuildStatus(String message, String state) {
  step([
        $class: "GitHubCommitStatusSetter",
        errorHandlers: [
            [
                $class: "ChangingBuildStatusErrorHandler",
                result: "UNSTABLE"
            ]
        ],
        statusResultSource: [
            $class: "ConditionalStatusResultSource",
            results: [
                [
                    $class: "AnyBuildResult",
                    message: message,
                    state: state
                ]
            ]
         ]
  ]);
}


pipeline {
    agent any
    triggers {
        githubPullRequests events: [commentPattern('run job please')], spec: '', triggerMode: 'HEAVY_HOOKS'
    }
    stages {
        stage("Do work") {
            steps {
//
//                script {
//                    // CHANGE_ID is set only for pull requests, so it is safe to access the pullRequest global variable
//                    if (env.CHANGE_ID) {
//                        matcher = pullRequest.title =~ /\[(.*)\] .*/
//                        echo "Here"
//                        if (matcher.matches()) {
//                            env.TITLE_PREFIX = matcher[0][1]
//
//                            if (!["slot-1"].contains(env.TITLE_PREFIX)) {
//                                currentBuild.result = "ABORTED"
//                                error("Invalid slot id in PR name")
//                            } else {
//                                setBuildStatus("Build in progress", "PENDING");
//                                pullRequest.comment("☁️🚀🌞 deploy & run integration tests in ${env.TITLE_PREFIX}")
//                            }
//                        } else {
//                            pullRequest.comment("Don't you forget to set slot-id param in PR title? 🤔")
//                            currentBuild.result = "ABORTED"
//                            error("Absent slot id in PR name")
//                        }
//                    }
//                }
//

                sh './main_script.sh'
            }
        }
        stage("build") {
            steps {
                script {
                    build(job: "test_parameters",
                        parameters:
                        [string(name: 'PATHTOJOB', value: "/I/parameterized/path"),
                        ])
                }
            }
        }
    }

    post {
        success {
            script {
                setBuildStatus("Build succeeded", "SUCCESS");
                if (env.CHANGE_ID) {
                    pullRequest.comment("🌞 Job completed successfully")
                }
            }
        }
//        failure {
//            script {
//                setBuildStatus("Build failed", "FAILURE");
//                // pullRequest.comment("Job completed with errors 🤔")
//            }
//        }
    }
}