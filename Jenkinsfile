pipeline {
    agent any
    
    triggers {
        pollSCM('*/5 * * * 1-5')
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
        stage('Conda Build ') {
            steps {
                echo "Building sample_project"
                sh  '''pwd 
                       conda build conda.recipe
                    '''
            }
        }
        
        stage('Test') {
            steps {
                echo 'Testing'
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
            echo 'This will always run'
        }
        success {
            echo 'This will run only if successful'
        }
        failure {
            echo 'This will run only if failed'
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
