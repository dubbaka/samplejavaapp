pipeline {
    agent any
    stages {
        stage('compile') {
		steps {
                	echo 'compiling..'
			git 'https://github.com/dubbaka/samplejavaapp.git'
			sh 'mvn compile'
            }
        }
        stage('codereview-pmd') {
			steps {
                		echo 'codereview..'
				sh 'mvn -P metrics pmd:pmd'
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
				sh 'mvn test'
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
				sh 'mvn cobertura:cobertura -Dcobertura.report.format=xml'
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
				sh 'mvn package'	
            }
			
        }
        stage('deploy') {
		   steps {
                	echo 'deploy'
			sh "deploy adapters: [tomcat9(credentialsId: 'tomcat9', path: '', url: 'http://172.17.0.13:8080')], contextPath: 'sampleapp', war: '**/*.war'"	
            	   }
	   			
        }     
    }
}
