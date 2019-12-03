#!/usr/bin/env groovy

def sqldir = '/home/oracle/'
def sqlfile

def versions

def choice1
def choice2

node {
    dir('/Users/michael/bin') {
        nodes = sh (script: 'sh ./list_nodes.sh', returnStdout: true).trim()
    }
}

pipeline {
    agent any
    
    // variables for the parameterised execution
    parameters {
      choice(choices: 'yes\nno', description: 'Startup the VM?', name: 'run_test_only')
      string(defaultValue: "10.65.5.35", description: 'Actifio Appliance', name: 'ActifioSky')  
      string(defaultValue: "admin", description: 'Actifio Username', name: 'ActifioUser')
      password(defaultValue: "actifio", description: 'Actifio Password', name: 'ActifioPass')
      booleanParam(defaultValue: true, description: ' ', name: 'BoolOption')
      choice(name: 'Nodes', choices:"${nodes}", description: "")
    }
   


    stages {
        stage ('Select SQL file') {
            
            steps {
                script {
                    def file_collection
                    file_collection = sh script: "ssh oracle@syd-ora12-1 'cd $sqldir; ls *.sql 2> /dev/null'", returnStdout: true
                    sqlfile = input message: 'Choose the right dump', ok: 'This One!', parameters: [choice(name: 'script file', choices: "${file_collection}", description: '')]
                }
            }
        }
        stage ('Run SQL script') {
            steps {

                sh "echo DEBUG: parameter foo = ${sqlfile} - ${run_test_only}"
                echo 'Running SQL scripts'
                sh script: "ssh oracle@syd-ora12-1 'export ORACLE_SID=demodb; export ORAENV_ASK=no; \
                            source oraenv -s ; sqlplus / as sysdba @$sqldir/$sqlfile'"
        
            }
        }
        stage ('Query DB1') {
            steps { 
                echo 'Query DB1'
                sh 'echo "Hello World"'
                sh '''
                  echo "Multiline shell steps"
                  ls -lah
                '''
                sh script: 'ssh oracle@syd-ora12-1 "export ORACLE_SID=demodb; export ORAENV_ASK=no;\
                            source oraenv -s ; sqlplus / as sysdba @/home/oracle/verify_db.sql"'
        
            }
        }
        stage ('Query DB2') {
            steps { 
                echo 'Query DB2'
                sh script: 'ssh oracle@syd-ora12-1 "export ORACLE_SID=demodb; export ORAENV_ASK=no;\
                            source oraenv -s ; sqlplus / as sysdba @/home/oracle/query_db.sql"'
        
            }
        }
    }
    
    post {
      always {
        echo 'I have finished'
        // deleteDir() // clean up workspace
      }
      success {
        echo 'I succeeded!'
      }
      unstable {
        echo 'I am unstable :/'
      }
      failure {
        echo 'I failed :('
      }
      changed {
        echo 'Things are different...'
      }
    }
}
