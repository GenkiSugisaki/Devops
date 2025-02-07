#!groovy

import groovy.json.JsonSlurperClassic

node {
    def HUB_ORG=env.HUB_ORG_DH
    def SFDC_HOST = env.SFDC_HOST_DH
    def JWT_KEY_CRED_ID = env.JWT_CRED_ID_DH
    def CONNECTED_APP_CONSUMER_KEY=env.CONNECTED_APP_CONSUMER_KEY_DH

    println 'KEY IS'
    println JWT_CRED_ID_DH
    println HUB_ORG_DH
    println SFDC_HOST_DH
    println CONNECTED_APP_CONSUMER_KEY_DH

    stage('checkout source') {
        // when running in multi-branch job, one must issue this command
        checkout scm
    }

    withCredentials([file(credentialsId: JWT_KEY_CRED_ID, variable: 'jwt_key_file')]) {
        stage('Deploy Code') {
            if (isUnix()) {
                rc = sh returnStatus: true, script: "sf auth:jwt:grant --clientid ${CONNECTED_APP_CONSUMER_KEY} --username ${HUB_ORG} --jwtkeyfile ${jwt_key_file} --serverurl ${SFDC_HOST}"
            } else {
                //bat "sf plugins:install salesforcedx@49.5.0"
                bat "sf update"
                //bat "sf auth:logout -u ${HUB_ORG} -p"
                rc = bat returnStatus: true, script: "sf auth:jwt:grant --clientid ${CONNECTED_APP_CONSUMER_KEY} --username ${HUB_ORG} --jwtkeyfile ${jwt_key_file} --serverurl ${SFDC_HOST}"
            }

            if (rc != 0) {
                println 'inside rc 0'
                error 'hub org authorization failed'
            } else {
                println 'rc not 0'
            }

            println rc

            // need to pull out assigned username
            if (isUnix()) {
                rmsg = sh returnStdout: true, script: "sf force:mdapi:deploy -d manifest/ -u ${HUB_ORG}"
            } else {
                rmsg = bat returnStdout: true, script: "sf force:mdapi:deploy -d manifest/ -u ${HUB_ORG}"
            }

            printf rmsg
            println('Hello from a Job DSL script!')
            println(rmsg)
        }
    }
}
