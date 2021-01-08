#!/usr/bin/env groovy

properties([disableConcurrentBuilds()])

pipeline {
    agent {
        label 'linux'
    }
    options {
        buildDiscarder(logRotator(numToKeepStr: '30', artifactNumToKeepStr: '30'))
        timestamps()
        ansiColor('xterm')
    }
    parameters {
        booleanParam(name: 'UpdateJfile', defaultValue: true, description: 'Update settings for deployment when Jenkinsfile was changed')
        choice(name: 'Task', choices: ['Install', 'Check'], description: 'Select what you need do')
    }
    stages {
        stage('CheckOut SCM') {
            steps {
                sh 'git checkout'
            }
        }
        stage('AnsibleVersion') {
            steps {
                sh 'ansible --version'
            }
        }
        stage('Run Setup Monitoring Ansible Playbook') {
            when {
                expression {
                    params.UpdateJfile ==~ 'false' && params.Task == 'Install'
                }
            }
            steps {
                ansiColor('xterm') {
                    dir("ansible_scripts") {
                        ansiblePlaybook(
                                playbook: 'monitoring-setup.yml',
                                inventory: 'hosts',
                                colorized: true)

                    }
                }
            }
        }
        stage('Run Check Monitoring Ansible Playbook') {
            when {
                expression {
                    params.UpdateJfile ==~ 'false' && params.Task == 'Check'
                }
            }
            steps {
                ansiColor('xterm') {
                    dir("ansible_scripts") {
                        ansiblePlaybook(
                                playbook: 'monitoring-check.yml',
                                inventory: 'hosts',
                                colorized: true)
                    }
                }
            }
        }
    }
}

