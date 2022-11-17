pipeline {
    agent docker
    stages {
        stage('compile') {
			steps {
                echo 'compiling..'
				git url: 'https://github.com/dubbaka/samplejavaapp'
				bat label: '', script: 'mvn compile'
            }
        }
        stage('codereview-pmd') {
			steps {
                echo 'codereview..'
				bat label: '', script: 'mvn -P metrics pmd:pmd'
            }
			post {
                success {
                    pmd canComputeNew: false, defaultEncoding: '', healthy: '', pattern: '**/pmd.xml', unHealthy: ''
                }
            }
			
        }
        stage('unit-test') {
			steps {
                echo 'codereview..'
				bat label: '', script: 'mvn test'
            }
			post {
                success {
                    junit 'target/surefire-reports/*.xml'
                }
            }
			
        }
        stage('metric-check') {
			steps {
                echo 'unit test..'
				bat label: '', script: 'mvn cobertura:cobertura -Dcobertura.report.format=xml'
            }
			post {
                success {
				cobertura autoUpdateHealth: false, autoUpdateStability: false, coberturaReportFile: 'target/site/cobertura/coverage.xml', conditionalCoverageTargets: '70, 0, 0', failUnhealthy: false, failUnstable: false, lineCoverageTargets: '80, 0, 0', maxNumberOfBuilds: 0, methodCoverageTargets: '80, 0, 0', onlyStable: false, sourceEncoding: 'ASCII', zoomCoverageChart: false                  
                }
            }
			
        }
        stage('package') {
			steps {
                echo 'metric-check..'
				bat label: '', script: 'mvn package'	
            }
			
        }
        stage('deploy') {
			steps {
                echo 'deploy'
				bat label: '', script: "deploy adapters: [tomcat9(credentialsId: 'tomcat9', path: '', url: 'http://172.17.0.13:8080')], contextPath: 'sampleapp', war: '**/*.war'"	
            }
			
        }     
    }
}