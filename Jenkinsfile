pipeline{
    agent {
        node{
            label 'Build'
            customWorkspace 'C:/Build'
        }
    }
    stages{
        stage('CheckOut'){
            steps{
                git branch: 'develop', url: 'https://github.com/rsridivya/simple_http_server.git'
            }  
        }
        stage('Build'){
            steps{
                bat "mvn package"
            }
        }
        stage('Code Coverage'){
            steps{
                bat "mvn clean clover:setup test clover:aggregate clover:clover install"
            }
        }
        stage('Publish'){
            steps{
                publishHTML([allowMissing: false, alwaysLinkToLastBuild: false, keepAll: false, reportDir: 'C:/Build/target/site/clover', reportFiles: 'index.html', reportName: 'Clover Report', reportTitles: ''])
                step([
                    $class: 'CloverPublisher',
                    cloverReportDir: 'C:/Build/target/site/clover',
                    cloverReportFileName: 'clover.xml',
                    healthyTarget: [methodCoverage: 70, conditionalCoverage: 80, statementCoverage: 80], // optional, default is: method=70, conditional=80, statement=80
                    unhealthyTarget: [methodCoverage: 50, conditionalCoverage: 50, statementCoverage: 50], // optional, default is none
                    failingTarget: [methodCoverage: 0, conditionalCoverage: 0, statementCoverage: 0]     // optional, default is none
                  ])
            }
        }
        stage('CheckIn'){
            steps{
                dir('C:/Build/target'){
                nexusArtifactUploader artifacts: [[artifactId: 'Http_Server', classifier: '', file: 'simple_http_server-0.0.1-SNAPSHOT.jar', type: 'jar']], credentialsId: 'Nexus', groupId: 'pkg.org', nexusUrl: '107.20.60.74:8081', nexusVersion: 'nexus3', protocol: 'http', repository: 'STS_Releases', version: '0.0.1'
                }  
                }
        }
    }
    
}
