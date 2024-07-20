pipeline {
    agent any
    stages {
        stage ('Checkout') {
            steps {
                git branch:'master', url: 'https://github.com/ScaleSec/vulnado.git'
            }
        }
        
        stage ('Build') {
            steps {
                sh '/var/jenkins_home/apache-maven-3.9.8/bin/mvn --batch-mode -V -U -e clean verify -Dsurefire.useFile=false -Dmaven.test.failure.ignore'
            }
        }

        stage ('Analysis') {
            steps {
                sh '/var/jenkins_home/apache-maven-3.9.8/bin/mvn --batch-mode -V -U -e checkstyle:checkstyle pmd:pmd pmd:cpd findbugs:findbugs'
            }
        }
    }

    post {
        always {
            junit testResults: '**/target/surefire-reports/TEST-*.xml'   // publishes test results
            recordIssues enabledForFailure: true, tools: [mavenConsole(), java(), javaDoc()]  // record warnings and errors from maven console 
            recordIssues enabledForFailure: true, tool: checkStyle()  // record code style violations
            recordIssues enabledForFailure: true, tool: spotBugs(pattern: '**/target/findbugsXml.xml') // record potential bugs
            recordIssues enabledForFailure: true, tool: cpd(pattern: '**/target/cpd.xml') // record code duplication reported by CPD
            recordIssues enabledForFailure: true, tool: pmdParser(pattern: '**/target/pmd.xml')  // record potential coding problems by PMD
        }
    }
}
