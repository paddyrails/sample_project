pipeline {
    agent any
    
    triggers {
        pollSCM('H/15 * * * 1-5')
    }

    options {
        skipDefaultCheckout(true)
        timestamps()
    }

    environment {
      PATH="/home/vagrant/anaconda3/bin:$PATH"
    }

    stages {
 
        stage ("Code pull"){
            steps{
                checkout scm
            }
        }

        stage('Static code metrics') {
            steps {
                echo "Raw metrics"
                sh  ''' source /home/vagrant/anaconda3/etc/profile.d/conda.sh
          	            conda activate
                        radon raw --json sample_project/risvmpy > raw_report.json
                        radon cc --json sample_project/irisvmpy > cc_report.json
                        radon mi --json sample_project/irisvmpy > mi_report.json
                    '''
                echo "Test coverage"
                sh  ''' source /home/vagrant/anaconda3/etc/profile.d/conda.sh
						conda activate
                        coverage run sample_project/irisvmpy/iris.py 1 1 2 3
                        python -m coverage xml -o reports/coverage.xml
                    '''
                echo "Style check"
                sh  ''' source /home/vagrant/anaconda3/etc/profile.d/conda.sh
						conda activate
                        pylint sample_project/irisvmpy || true
                    '''
            }
		}
		
		/*stage ("Cobertura - Extract test results") {
			steps {
                echo "Old way extract metrics"
                sh  ''' source /home/vagrant/anaconda3/etc/profile.d/conda.sh
				    '''
			}
			post {
                always {
			       step ([$class: 'CoberturaPublisher',
						   autoUpdateHealth: false,
						   autoUpdateStability: false,
						   coberturaReportFile: 'reports/coverage.xml',
		    			   failNoReports: false,
						   failUnhealthy: false,
			    		   failUnstable: false,
						   maxNumberOfBuilds: 10,
						   onlyStable: false,
						   sourceEncoding: 'ASCII',
						   zoomCoverageChart: false])
				}
			}
        }*/
		
		stage('Unit tests') {
            steps {
                sh  ''' source /home/vagrant/anaconda3/etc/profile.d/conda.sh
				        conda activate
                        python3.7 -m pytest --verbose --junit-xml reports/unit_tests.xml
                    '''
            }
            post {
                always {
                    // Archive unit tests for the future
                    junit allowEmptyResults: true, testResults: 'reports/unit_tests.xml'
                }
            }
        }
		
		stage('Acceptance tests') {
            steps {
                sh  ''' source /home/vagrant/anaconda3/etc/profile.d/conda.sh
				        conda activate
                        behave -f json.pretty -o ./reports/acceptance.json || true
                    '''
            }
            /*post {
                always {
                    cucumber (buildStatus: 'SUCCESS',
                    fileIncludePattern: '**//*.json',
                    jsonReportDirectory: './reports/',
                    parallelTesting: true,
                    sortingMethod: 'ALPHABETICAL')
                }
            }*/
        }

        stage('Conda Build ') {
            steps {
                echo "Building sample_project"
                sh  '''conda build conda.recipe
                    '''
            }
        }
        
        stage('Deploy') {
            steps {
                echo 'Deploying'
            }
        }
    }
    post {
        always {
			echo 'Build completed'
        }
        success {
            echo 'Great this build is successful'
        }
        failure {
            echo 'And it failed'
        }
        unstable {
            echo 'This will run only if the run was marked as unstable'
        }
        changed {
            echo 'This will run only if the state of the Pipeline has changed'
            echo 'For example, if the Pipeline was previously failing but is now successful'
        }
    }
}
