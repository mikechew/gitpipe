#!/usr/bin/env groovy

pipeline {
   agent any

    stages {
        stage('Test') {
            parallel {
                stage('Test On master') {

                    steps {
                      echo "unit tests on master"
                        sh "ls -lrt && touch build.txt"
                        sh "whoami && hostname"
                        sleep(time:5,unit:"SECONDS")
                    }
                }
                stage('Test On dev-ubuntu01') {

                    steps {
                      echo "integration tests on dev-ubuntu01"
                        sh "ls -lrt && touch build.txt"
                        sh "whoami && hostname"
                        sleep(time:5,unit:"SECONDS")
                    }
                }
            }
        }
    }
}